---
layout: post
date: 06-03-2023
author: Andrzej Jóźwiak
title: At the mountains of programming madness
tags: kotlin programming annotations annotation-processor kotlin-poet java-poet android
disqus: true
---

*“It is absolutely necessary, for the peace and safety of mankind, that some of earth's dark, dead corners and unplumbed depths be let alone; lest sleeping abnormalities wake to resurgent life, and blasphemously surviving nightmares squirm and splash out of their black lairs to newer and wider conquests.”*

It is a nice quote from one of the H.P.Lovecraft's book titled "At the mountains of mandness", I was reminded of it while working on a bug in the code responsible for generating obfuscation mechanism with Kotlin Poet.

As with any type of a tool that does some magic in the name of making our tasks easier there comes the possible danger of it failing in a very cryptic and incomprehensible way.

I won't go into detail how my obfuscation algorithm works suffice to say that it takes a string and returns an obfuscated result.

For a string like `jug lodz is the best` it would return a `4.ef hh4.:{1a]dpfxA9`. So one would say it is a simple form of encryption rather then obfuscation. The main idea is to avoid keeping api keys, tokens, passwords, app ids and other types of fragile information out in the open in the APK.

Usually in Android it is easy to keep everything in the `/res/values/strings.xml` or a similar file in resources. The problem is that such file is easily extractable with tools like `apktool`. So to avoid it being straightforward (ofc if someone is dedicated enough he will get them) and not make anyone life easier we decided to obfuscate them so at least they are not readily available after decompiling the APK.

My solution consists of an special annotation that is added to a property or a function that should return some data that should be kept as obfuscated. A funny thing is that I found an open source library that is doing the exact same thing called [KCiphertext](https://github.com/atomikpanda/KCiphertext).

Let's define such annotation.

```kotlin
@Retention(AnnotationRetention.SOURCE)
@Target(AnnotationTarget.PROPERTY, AnnotationTarget.FUNCTION)
annotation class Secret(
    val value: String,
)
```

We wan't the annotation to not be kept during runtime. We are also interested in it be used only for Kotlin's properties or functions. For simplicities sake it will only keep a bare `String` as value.

Now an annotation processor is needed. The simplest approach would be to extend the `AbstractProcessor` and implement it's methods.

```kotlin
@SupportedSourceVersion(SourceVersion.RELEASE_8)
class SecretProcessor : AbstractProcessor() {
    override fun process(
        annotations: MutableSet<out TypeElement>?,
        roundEnv: RoundEnvironment?
    ): Boolean {
        // implementation
    }
}
```

Processing of annotation is simple. The usual algorithm is:
1. Get all the elements annotated with `Secret`
2. Check if the annotation is used according to the specification meaning the `ElementKind` is for certain `ElementKind.METHOD`
3. For each valid element:
    1. Get secret value that should be obfuscated/encoded
    2. Create a `TypeSpec.Builder` for each element
    3. Generate the code that will return the deobfuscated value
    4. Save the code in a dedicated file

Pretty straightforward and simple.

```kotlin
class SecretProcessor : AbstractProcessor() {
    override fun process(
        annotations: MutableSet<out TypeElement>?,
        roundEnv: RoundEnvironment?
    ): Boolean {
        roundEnv
            ?.getElementsAnnotatedWith(Secret::class.java)
            ?.onEach { element ->
                if (element.kind != ElementKind.METHOD) {
                    processingEnv.messager.printMessage(
                        Diagnostic.Kind.ERROR,
                        "Only properties or functions can be annotated with @Secret but ${element.simpleName} is ${element.kind}!"
                    )
                    return@process true
                }

                // 1. Get the secret
                // 2. Create a type builder
                // 3. Save to file
            }
    }
}
```

All of such solutions are based on the idea of storing the encoded data in a form of a byte array and decoding it on the fly. It means that with using [KotlinPoet](https://square.github.io/kotlinpoet/) one will play a lot with strings, mainly because of `CodeBlock` type used by the library.

```kotlin
val list = listOf(1, 2, 3)
val codeBlock = CodeBlock.builder()
codeBlock.add("val value = 42")
codeBlock.add("val aList = listOf(")
codeBlock.add(list.joinToString(separator = ", "))
codeBlock.add(")")
// ... other code added
codeBlock.build()
```

The code above will result with a generated code of:

```kotlin
val value = 42
val aList = listOf(
    1, 2, 3
)
```

A more complicated example from the [KCiphertext](https://github.com/atomikpanda/KCiphertext/blob/master/KCiphertextExample/aprocessor/src/main/java/com/baileyseymour/kciphertextaprocessor/AnnotationProcessor.kt) library would be:

```kotlin
FunSpec.builder("decrypt").apply {
    addModifiers(KModifier.INLINE, KModifier.PUBLIC)
    addAnnotation(AnnotationSpec.builder(Suppress::class).addMember("\"NOTHING_TO_INLINE\"").build())
    receiver(classReceiver)
    addParameter("byteArray", ByteArray::class)
    returns(ByteArray::class)
    addStatement("val byteList = byteArray.asList()")
    addStatement("val adjusted = byteList.run {\n" +
                 "        this.map { (it - 1).toByte() }\n" +
                 "    }")
    addStatement("return adjusted.toByteArray()")
}
```

I was going to quickly understand that there is more then meets the eye soon enough.

After I finished implementing the processor, I immediately started adding annotations for the secrets I wanted to be obfuscated. Everything went fine. Adding them one by one, project was building until I got an error.

```
Execution failed for task ':app:kaptAlphaDebugKotlin'.
> A failure occurred while executing org.jetbrains.kotlin.gradle.internal.KaptWithoutKotlincTask$KaptExecutionWorkAction
   > java.lang.reflect.InvocationTargetException (no error message)
```

Initially I thought that maybe something is wrong with how I am generating code. As the problem started occuring for the last thing I added I wanted to pinpoint the culprit first.

```kotlin
@Secret("some super secret thingy")
fun getSuperSecretStuff(): String

@Secret("Even more secret thing")
fun getEvenMoreSecretStuff(): String
```

Initially I just commented out one of the freshly added things.

```kotlin
@Secret("some super secret thingy")
fun getSuperSecretStuff(): String

// @Secret("Even more secret thing")
// fun getEvenMoreSecretStuff(): String
```

And the build started working. I had two possibilities:
1. Either something is wrong with the code and it breakes when it needs to prepare secrets for two functions
2. Or something is wrong with the second function

I went through the code again to check if there are any mistakes but I found nothing suspicious. So I changed which function is commented out.

```kotlin
// @Secret("some super secret thingy")
// fun getSuperSecretStuff(): String

@Secret("Even more secret thing")
fun getEvenMoreSecretStuff(): String
```

And it failed. Ok we are getting somewhere. The main problem is that the returned exception doesn't bring much information. So two new possibilities opened to me:
1. Do a good old `printf` debugging
2. Try to attach a debugger to a running annotation processor

I started with option one. Adding logging was simple.

```kotlin
processingEnv.messager.printMessage(
    Diagnostic.Kind.NOTE,
    "Logging here"
)
```

I logged almost every step of the process without much success. The file was created but the whole solution crashed before it could write the content.

I started experimenting with the secret values.
1. Maybe it is too short? Stupid idea but let's check. No
2. Maybe it is something related to the content? I used a different secret. It worked fine. Yes
3. The text of the secret was super short something like `cat456f`. So one by one I started removing the chars `cat456f` -> Failed -> `cat456` -> Failed -> `cat45` -> Failed -> `cat4` -> Failed -> `cat` -> Failed -> `ca` -> Works! Something with the letter `t`? Maybe?

I confirmed that the letter `t` if present on the third position of the obfuscated string will cause the exception. Just in case I experimented with a lot of different strings and they all worked fine.

I retruned to examining my obfuscation code. It was fine. It produced the values based on what was supplied. For the letter `t` on that particular position it produced a very peculiar `char` which is `«`. I did not think about it much about it. All I knew was that I need to debug my annotation processor and catch the exception directly in its source.

So how to debug an annotation processor?

There are a few steps, all depending on the IDE one is using. Here are the steps I used with the latest Android Studio.

1. Create a new Remote JVM Debug configuration (Run/Debug Configurations > + > Remote JVM Debug)
2. Leave the defaults unchanged, maybe change the name so it is more obvious what is the configuration for
3. Add `kapt.use.worker.api=true` to `gradle.properties`, without this line the code won't stop on breakpoints
4. Add the desired breakpoints in the code

Now the only thing to do is to run the `assemble` in Gradle.

```
./gradlew --no-daemon -Dorg.gradle.debug=true -Dkotlin.daemon.jvm.options="-Xdebug,-Xrunjdwp:transport=dt_socket\,address=5005\,server=y\,suspend=n" :clean assemble
```

Debugging allowed me to greatly pinpoint the problem. It seemed that the code for writing to file in KotlinPoet's `FileSpec` was crashing.

```kotlin
@Throws(IOException::class)
fun writeTo(directory: Path) {
    require(Files.notExists(directory) || Files.isDirectory(directory)) {
        "path $directory exists but is not a directory."
    }
    var outputDirectory = directory
    if (packageName.isNotEmpty()) {
        for (packageComponent in packageName.split('.').dropLastWhile { it.isEmpty() }) {
        outputDirectory = outputDirectory.resolve(packageComponent)
        }
    }

    Files.createDirectories(outputDirectory)

    val outputPath = outputDirectory.resolve("$name.kt")
    OutputStreamWriter(Files.newOutputStream(outputPath), UTF_8).use { writer -> writeTo(writer) }
}
```

By changing the breakpoint configuration to break on exception I managed to pinpoint the problem to `CodeWriter` `emitCode` method.

It thrown an `IllegalArgumentException` with a very strange message that had a familiar element in it.

```kotlin
"statement enter « followed by statement enter «"
```

I recognized the same char that the letter `t` was encoded to. This strange `«` was causing the problem? Quick glance at the methods code showed the problem:

```kotlin
"⇥" -> indent()
"⇤" -> unindent()
"«" -> {
    check(statementLine == -1) { "statement enter « followed by statement enter «" }
    statementLine = 0
}

"»" -> {
    check(statementLine != -1) { "statement exit » has no matching statement enter «" }
    if (statementLine > 0) {
    unindent(2) // End a multi-line statement. Decrease the indentation level.
    }
    statementLine = -1
}
```

It seems that the KotlinPoet library is using these "special" characters. The library is interpreting special strings like `%T` or `%M` but I did not recall ever reading about `«` in the docs and how it can affect the generated code.

So in the end, the obfuscation code was to blame. I had to change the algorithm to avoid generating `⇥`, `⇤`, `«` or `»` in the future. After all of this I had very mixed feelings. On one hand I was happy that I finally found the exact problem and a solution for it, but on the other I was sad and a bit angry about the lost time. The behaviour could be better documented and would be nice to get a better exception but I guess this is another lesson learned.

