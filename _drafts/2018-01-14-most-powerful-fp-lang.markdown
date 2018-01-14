---
layout: post
date: 14-01-2018
author: Andrzej Jóźwiak
title: Araising as one of the most powerful?
tags: kotlin fp design
disqus: true
---

It's been around 4 months since [Mobilization 2017](http://2017.mobilization.pl/) and I'm still watching the talks I missed during the conference. One of the presentations I really enjoyed was "Functional approach to Android architecture using Kotlin" by Jorge Castillo.

I was surprised it's available not only on [JUG Łódź youtube channel](https://www.youtube.com/channel/UCS5yGVNI9HfkGz9EngOLqIQ) but also on [academy.realm.io](https://academy.realm.io/posts/mobilization-2017-jorge-castillo-functional-android-architecture-kotlin/) with a full transcript (sic!). I strongly suggest you check it out on their site.

Everything would be fine if not for one sentence in the description of the talk:

> "Kotlin is arising as one of the most powerful functional languages".

Imho this is a bit of exaggeration, especially unfair for Scala, Clojure and ETA. I even checked what did we have in the description on the Mobilization site:

> "Modern languages with functional colours are mainstream lately. Kotlin is arising as one of the most powerful ones"

Ok, so it was "most powerful with functional colours". Someone just did an unfortunate mental shortcut when writing the description, regardless this "most powerful" thing made me think. Is Kotlin really that powerful and how one defines programming language power?

Starting with advantages. Kotlin's syntax is much more concise than Java's (for me it's the main selling point on the JVM, after years of working with Java 6). Language allows for standalone functions, they are still objects in disguise (this is JVM we are talking about here) but there is a really nice syntax for declaring and invoking them. Is this enough to contend the title of "most powerful" functional language?

**I think not.** Kotlin is limited by its syntax and design decisions, in other words, FP was IMHO not it's primary vision of usage. (I don't say that was necessarily a bad decision!)

FP is mainly about functions and their composition, let's look how Kotlin fairs in this department.

```kotlin
fun foo(s: String): Int =
        s.length

fun bar(i: Int): Boolean =
        i.mod(2) == 0
```

Now I want to have a function from type `String` to type `Boolean`, normally we should be able to achieve it with function composition. Unfortunately, Kotlin has none in the standard library. This is a real shame as Java 8 that introduced `Function` interface gave programmers `andThen` and `compose` methods in the said interface.

So what choices do we have? Either we invoke those two functions and compose them in place of the call:

```kotlin
fun baz1(s: String): Boolean =
        bar(foo(s))
```

or we define our own method of function composition. Thanks to the feature of extension methods it's super simple in Kotlin.

```kotlin
infix fun <A, B, C> ((A) -> B).andThen(f: (B) -> C): (A) -> C =
        { a: A ->
            f(this(a))
        }
```

now we can write our baz function differently:

```kotlin
val baz2 = ::foo andThen ::bar
```

It was really easy (this is why I like this language) but on the other hand, the composition is missing from the standard library. Someone asked about it on the [official forums](https://discuss.kotlinlang.org/t/function-composition-in-standard-library/1683) and got a reply:

> "No, it’s not in the standard library. Function composition is very important in certain programming styles, and almost never occurs in others."

This quote is not an accusation from my side, just a confirmation that FP was not the goal of the designers. There are wonderful libraries that fill this gap like [funKtionale](https://github.com/MarioAriasC/funKTionale) or [arrow](http://arrow-kt.io/) (former kategory) which I strongly advise to check.

Ok, so no function composition by default, what about currying and partial application?

Let's say I have a function for adding two `Int` values:

```kotlin
fun plus(a: Int, b: Int) =
        a + b
```

Now I would like to partially apply it to the first argument, let's say a `1`, to get a one-argument function that is always adding `1` to whatever else is passed to it. Writing something like:

```kotlin
val plus1: (Int) -> Int = curriedPlus(1)
```

is just not possible, unless we do some magic first:

```kotlin
// this function takes a function (A , B) -> C and returns (A) -> (B) -> C
fun <A, B, C> curry(f: (A, B) -> C): (A) -> (B) -> C =
        { a: A ->
            { b: B ->
                f(a, b)
            }
        }
```

and now we can do:

```kotlin
val curriedPlus: (Int) -> (Int) -> Int = curry(::plus)
val plus1: (Int) -> Int = curriedPlus(1)
```

What about doing this the other way around (from curried form to uncurried)? We need to write it ourselves again:

```kotlin
fun <A, B, C> uncurry(f: (A) -> (B) -> C): (A, B) -> C =
        { a: A, b: B ->
            f(a)(b)
        }

val uncurriedPlus: (Int, Int) -> Int = uncurry(curriedPlus)
```

Yet again external libraries have our back [here](https://github.com/arrow-kt/arrow/blob/3fdf22311dbdf07c68ee73ab05cd2f21561d6636/arrow-core/src/main/kotlin/arrow/core/predef.kt) and [here](https://github.com/MarioAriasC/funKTionale/wiki/Currying). I know that the beauty is in the eye of the beholder, but there is some ugliness in Kotlin syntax if we want to work with curried forms of functions. It's visible in the `uncurry` implementation, where we had to call the curried function like this `f(a)(b)`.

I think Brian Lonsdorf called it "weird butt looking thing" in one his [JS FP talks](https://www.youtube.com/watch?v=m3svKOdZijA), it's hard to disagree with the description. Still, I understand why it looks like this and it's much much much (I can't even express how much) better than Java's `f.apply(a).apply(b)`.

There is also one more ugly thing, which is the number of bracers used. This is a conscious language design decision, but a long enough curried function will look like a "bracer orgy" and readability might suffer (as usual your mileage might vary).

I think that **DSL's** (Domain Sepcific Language) where the primary focus of the language. When learning Kotlin from its great reference docs, it's easy to understand the **DSL** heritage after reading "type-safe" [builders](https://kotlinlang.org/docs/reference/type-safe-builders.html) example. The syntactic sugar was made especially for that (did Kotlin designers love Groovy?).

A minute of browsing through the available API allows finding a less complicated example of this "sugar" in action:

```kotlin
public inline fun <R> synchronized(lock: Any, block: () -> R): R {
    @Suppress("NON_PUBLIC_CALL_FROM_PUBLIC_INLINE", "INVISIBLE_MEMBER")
    monitorEnter(lock)
    try {
        return block()
    }
    finally {
        @Suppress("NON_PUBLIC_CALL_FROM_PUBLIC_INLINE", "INVISIBLE_MEMBER")
        monitorExit(lock)
    }
}
```

There are two ways of calling this function: "sugar-free" (forgive me this pun) and "sugar-full".

```kotlin
val myLock = Any()
val locked1 = synchronized(myLock, { 42 })

// or better (sugary):

val locked2 = synchronized(myLock) {
    42
}
```

This strangely resembles `synchronized` block in Java. Kotlin doesn't have a reserved "synchronized" keyword, everything can be achieved through a sly function and some sugar (I like it). This is explained in the docs:

> In Kotlin, there is a convention that if the last parameter to a function is a function, and you're passing a lambda expression as the corresponding argument, you can specify it outside of parentheses:
>
> lock (lock) {
>    sharedResource.operation()
>}

Due to mentioned **DSL** design decision any higher-order function that takes a function as the first argument looks hideous with a lambda.

```kotlin
fun qux1(f: (String) -> Int, s: String): Int =
        f(s)
```

using it:

```kotlin
val qux1v = qux1({ s -> s.length }, "This looks ugly")
```

In a curried form `qux` function could be invoked differently (we will use our `curry` function we defined earlier):

```kotlin
// specifying the qux2 type is optional, but I added it for better understanding
val qux2: ((String) -> Int) -> (String) -> Int = curry(::qux1)
```

This way it can be used as:

```kotlin
val qux2v: Int = qux2 { it.length }("This is less ugly")
```

We are back to our "weird butt looking" notation, where one of our buttocks is now built from bracers. Like I said this is a matter of taste (I don't like it but I don't hate it).

Let's get this **DSL** stuff out of the way now cause there is one last thing I would like to talk about. Kotlin is primarily an Object Oriented language, extension functions are a good hint at that. The [motivation](https://kotlinlang.org/docs/reference/extensions.html#motivation) behind creating them were Util classes known from Java.

So instead of doing `Collections.max(list)` we could do `list.max()`. This is much more in line with OO way of thinking but less with FP. I can't express how grateful I am for adding those. Working with Java 6 collections (some of us still can't use the Stream API) was a breeze thanks to Kotlin.

Going back to the function composition example, it would be nice to reason about functions without the explicit and immediate need of thinking about their data. Let's take `List.map` function as an example. It returns a list containing the results of applying a function to each element in the original list. Let's rewrite so it's not an extension anymore.

```kotlin
//behind the scenes the extension function, but we care more for the signature
fun <T, R> map1(list: List<T>, function: (T) -> R): List<R> =
        list.map(function)
```

It takes a list as the first argument and function as the last, allowing us to use the **DSL** sugar.

```kotlin
val map1v1: List<Int> = map1(listOf("This", "is a", "list")) {
    it.length
}
```

But what if we would need/like to compose it? Let's say we need a function that takes a list of strings and returns another list containing only the first character of each string.

Our function that returns first character can look like this:

```kotlin
// I'm using existing extensions just to have a working example
fun firstCharacter(string: String): String =
        string.take(1)

// getting a value is easy but how to create the needed function?
val firstLetterV1: List<String> = map1(listOf("This", "is a", "list")) {
    firstCharacter(it)
}
```
I know, I know we can now simply do:

```kotlin
fun firstLetters(list: List<String>): List<String> =
        map1(list) {
            firstCharacter(it)
        }
```

This shows that we still don't treat functions as first class citizens of our code. It's probably due to how we are taught at school but there is mental barier to treating functions as values. Let's try and specify it differently. We need to redefine our `map1` function:
- the order of arguments needs to be swapped (function should go first)
- it needs to be curried by default (partial application should be possible)

```kotlin
fun <T, R> map2(function: (T) -> R): (List<T>) -> List<R> =
        { list ->
            list.map(function)
        }
```

Let's give this new version a spin and use it:

```kotlin
val firstLetterV2: List<String> = map2(::firstCharacter)(listOf("This", "is a", "list"))
```

We can now define the `firstLetters` function as a composition (no mentioning about the `List<String>` anywhere):

```kotlin
// type for firsLetter function is optional and can be inferred
val firstLetters: (List<String>) -> List<String> = map2(::firstCharacter)
```

You wouldn't believe but compiler is really smart. It knows that if our `firstCharacter` function takes a `String` and returns a `String`, then `map2` needs to take `List<String>` and return a `List<String>` also. There is no need to specify anything else, and we can write `firstLetters` function in a "point-free" form (at least as much as possible). I first learned about the "point-free" notation when learning Haskell, it originates from the fact that function arguments are often called "points" thus "point-free".

We can now pass our `firstLetters` function wherever a `List<String> -> List<String>` is needed, we can compose it to create more complicated functions, we can run it etc.

So what does all of this say about the Kotlin's power?

I won't deny that I would pick it over Java any day of a month, but I can't agree its "the most powerful functional language" out there available.

We managed to add all the missing but needed functionality through library functions and extensions, but the language and its stdlib didn't support everything out the box.

Syntax tailored more towards **DSL's** might prove less readable in FP context, but it doesn't mean it won't be possible to do FP with it (as we easily demonstrated). Kotlin does not equal effortless FP but is powerful in its own right.
