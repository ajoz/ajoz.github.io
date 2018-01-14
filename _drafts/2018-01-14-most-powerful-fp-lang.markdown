---
layout: post
date: 14-01-2018
author: Andrzej Jóźwiak
title: Araising as one of the most powerful?
tags: kotlin fp design
disqus: true
---

It's been around 4 months since Mobilization 2017 and I'm still watching the talks I missed during the conference. One of the presentations I really enjoyed was "Functional approach to Android architecture using Kotlin" by Jorge Castillo.

I was surprised it's available not only on JUG Łódź youtube channel but also on realm.io with a full transcript (sic!). I strongly suggest you check it out on their site.

Everything would be fine if not for one sentence in the description of the talk:

> "Kotlin is arising as one of the most powerful functional languages".

Imho this is a bit of exaggeration, especially unfair for Scala, Clojure and ETA. I even checked what did we have in the description on the Mobilization site:

> "Modern languages with functional colours are mainstream lately. Kotlin is arising as one of the most powerful ones"

Ok, so it was "most powerful with functional colours". Someone just did an unfortunate mental shortcut when writing the description, regardless this "most powerful" thing made me think.

Is Kotlin really that powerful and how one defines programming language power?

Starting with advantages.

Kotlin's syntax is much more concise than Java's (for me it's the main selling point on the JVM, after years of working with Java 6).

Language allows for standalone functions, they are still objects in disguise (this is JVM we are talking about here) but there is a really nice syntax for declaring and invoking them.

Is this enough to contend the title of "most powerful" functional language?

I think not. Kotlin is limited by its syntax and design decisions, in other words, FP was IMHO not it's primary vision of usage. (I don't say that was a bad decision!)

FP is mainly about functions and their composition, let's look how Kotlin fairs in this department.

```kotlin
fun foo(s: String): Int =
        s.length

fun bar(i: Int): Boolean =
        i.mod(2) == 0
```

Now I want to have a function from type String to type Boolean, notmallt we should be able to achieve it with function composition. Unfortunately, Kotlin has none in the standard library. This is a real shame as Java 8 that introduced Function interface gave programmers andThen and compose methods in the said interface.

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

It was really easy (this is why I like this language) but on the other hand, the composition is missing from the standard library. Someone asked about it on the official forums and got a reply:

> "No, it’s not in the standard library. Function composition is very important in certain programming styles, and almost never occurs in others."

This quote is not an accusation from my side, just a confirmation that FP was not the goal of the designers. There are wonderful libraries that fill this gap like funKtionale or arrow (former Kategory) which I strongly advise to check.

Ok, so no function composition by default, what about currying and partial application?

Let's say I have a function for adding two Int values:

```kotlin
fun plus(a: Int, b: Int) =
        a + b
```

Now I would like to partially apply it to the first argument, let's say one, to get a one-argument function that is always adding one to whatever else is passed to it. Writing something like:

```kotlin
val plus1: (Int) -> Int = curriedPlus(1)
```

is just not possible, unless we do some extension magic again:

```kotlin
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
val plus1 = curriedPlus(1)
```

What about doing this the other way around? We need to write it ourselves again:

```kotlin
fun <A, B, C> uncurry(f: (A) -> (B) -> C): (A, B) -> C =
        { a: A, b: B ->
            f(a)(b)
        }

val uncurriedPlus: (Int, Int) -> Int = uncurry(curriedPlus)
```

Yet again external libraries have our back here. I know that the beauty is in the eye of the beholder, but there is some ugliness in Kotlin syntax if we want to work with curried forms of functions. It's visible in the uncurry implementation, where we had to call the curried function like this `f(a)(b)`.

I think Brian Lonsdorf called it "weird butt looking thing" in one his JS FP talks, it's hard to disagree with the description. Still, I understand why it looks like this and it's much much much (I can't even express how much) better than Java's `f.apply(a).apply(b)`.

There is also one more ugly thing, which is the number of bracers used. This is a conscious language design decision, but a long enough curried function will look like a "bracer orgy" and readability might suffer (as usual your mileage might vary).

I think that DSL's where the primary focus of the language. When learning Kotlin from its great reference docs, it's easy to understand the DSL heritage after reading "type-safe" builders example. The syntactic sugar was made especially for that.

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

This strangely resembles synchronized block in Java. Kotlin doesn't have a reserved "synchronized" keyword, everything can be achieved through a sly function and some sugar (I like it). This is explained in the docs:

> In Kotlin, there is a convention that if the last parameter to a function is a function, and you're passing a lambda expression as the corresponding argument, you can specify it outside of parentheses:
>
> lock (lock) {
>    sharedResource.operation()
>}

Due to mentioned DSL design decision any higher-order function that takes a function as the first argument looks hideous with a lambda.
