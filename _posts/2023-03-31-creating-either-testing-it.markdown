---
layout: post
date: 31-03-2023
author: Andrzej Jóźwiak
title: Creating an Either and testing with it!
tags: kotlin fp functional-programming testing tests junit kotest
disqus: true
---

In one of my projects I wanted to express that functions can fail. As usual with any JVM/Android code I had an option to use `Exceptions` or `nulls`. Still I wanted to have a better way to express the failure. I decided to create a type that can be used to indicate both a success and failure. This would be `Either`.

## Creating an Either type

`Either` is a sum type (also known as a tagged union, discriminated union, variant record, choice type, coproduct). In simpler words it is just a data structure that is used to hold a value that could take on different types. The types are fixed, cannot be extended, the data structure can only be in one of the cases at any one time.

If `Either` is supposed to express existence of two distnct values, let's assume that it has a `Left` and `Right` cases. In Kotlin it can easily be expressed with a sealed hierarchy i.e. sealed class or a sealed interface.

```kotlin
sealed interface Either {
    object Left : Either
    object Right : Either
}
```

`Either` defined above is not very useful as the objects defined in the sealed interface do not allow to allow one to return information. In my case I wanted to use this data structure to handle a `GameFailure` and `GameState`. 

```kotlin
sealed interface Either {
    data class Left(value: GameFailure): Either
    data class Right(value: GameState): Either
}
```

This seems like enough but does not allow to express anything else. Generics can help with making the solution more generic (sic!).

```kotlin
sealed class Either<out A, out B> {
    class Left<A>(val value: A) : Either<A, Nothing>()
    class Right<B>(val value: B) : Either<Nothing, B>()
}
```

In Kotlin we can use `Nothing` for the type that is not needed as `Nothing` is derived from every type. 

## Using the Either type

Although Kotlin allows us to use `when` expression, it would be much better to use a built in function that will always force one using `Either` to handle both `Right` and `Left` cases. Let's call such function `fold` it will change the values represented by `Either` into some specified type through the use of two specified functions.

```kotlin
fun <A, B, C> Either<A, B>.fold(
    left: (A) -> C,
    right: (B) -> C,
): C =
    when (this) {
        is Either.Left -> left(value)
        is Either.Right -> right(value)
    }
```

The usage is very simple.

```kotlin
val result: Either<GameFailure, GameState> = Either.Left(value = NotEnoughPlayers)

val message = 
    result.fold(
        left = { "Failure during game start: $it" },
        right = { "Game started suscessfully!" }
    )

println(message)
```

In the example above an `Either<GameFailure, GameState>` is "folded" to a `String` message that can be printed to the screen.

## Asserting expected Either in JUnit

How should a function that returns an `Either` be tested? As the data type is a sealed hierarchy we can write a `when` in each test and check what we need.

```kotlin
class SomeTest {
    @Test
    fun `This is a tested scenario`() {
        // given:
        val expected = ...
        val tested = TestedClass()

        // when:
        val actual: Either<Failure, Foo> = tested.doThings()

        // then:
        when (actual) {
            is Either.Left -> {
                // fail if this is not expected
            }
            is Either.Right -> {
                // fail if this is not expected
            }
        }
    }
}
```

`Either` could be folded into a `Boolean` and the result could be combined with some `assertTrue` or `assertFalse`.

```kotlin
class SomeTest {
    @Test
    fun `This is a tested scenario`() {
        // given:
        val expected = ...
        val tested = TestedClass()

        // when:
        val actual: Either<Failure, Foo> = tested.doThings()

        // then:
        val result =
            when (actual) {
                is Either.Left -> {
                    false
                }
                is Either.Right -> {
                    // check the expected value
                    it == Foo(42) && it.isSomethingImportant()
                }
            }

        assertTrue(result)
    }
}
```

This is a lot of boilerplate code needed for each test. Using the bare `assertTrue` or `assertFalse` does not bring meaningful information about the content of the `Either`. To solve both problems a custom `assert` is needed.

Usually the asserts from JUnit or Kotlin test libraries have a similar structure. An assert function expects an actual value, expected value and an optional message.

In case of our `Either` we can specify the new assert as:

```kotlin
@Throws(AssertionError::class)
fun <A, B> assertRight(
    actual: Either<A, B>,
    expected: B,
    message: String? = null,
) { }
```

This definition is very similar to `assertEquals`

```kotlin
fun <T> assertEquals(
    expected: T,
    actual: T,
    message: String? = null
): Unit
```

We can leave it as such but there is a problem with the newly specified assert. In case of `assertEquals` it compares `expected` with the `actual`, both have the same type. In case of `assertRight`, the `actual` is `Either<A, B>` and `expected` is just `B`, there is no symmetry. Besides the obious lack of symmetry we see that we can only check for equality.

```kotlin
@Throws(AssertionError::class)
fun <A, B> assertRight(
    actual: Either<A, B>,
    message: String? = null,
    check: (B) -> Boolean = { false },
) { }
```

Instead of passing a value of type `B`, a lambda of type `(B) -> Boolean` is passed. This allows checking for more then equality and building more complex predicates that can be used for testing.


```kotlin
@Throws(AssertionError::class)
fun <A, B> assertRight(
    actual: Either<A, B>,
    message: String? = null,
    check: (B) -> Boolean = { false },
) {
    actual.fold(
        left = {
            // we expected Right, so we fail immediately when getting a Left
            fail(
                message = "Expected a Right but got a Left: $it"
            )
        },
        right = {
            // we use a custom message if supplied or the default if not available
            assertTrue(
                actual = check(it),
                message = message ?: "Right: $it did not meet the requirements!"
            )
        },
    )
}
```

Using the implemented `assertRight` will remove the boiler plate and give us a lot of information through the builtin messages.

```kotlin
class SomeTest {
    @Test
    fun `This is a tested scenario`() {
        // given:
        val expected = ...
        val tested = TestedClass()

        // when:
        val actual: Either<Failure, Foo> = tested.doThings()

        // then:
        assertRight(actual) {
            it == Foo(42) && it.isSomethingImportant()
        }
    }
}
```

## Asserting expected Either in Kotest

JUnit assert was a thing that everyone is used to. One can write an assert function through the use of other already existing assert functions. In case of [Kotest](https://kotest.io/) library one needs to conform to the style introduced by the library and also its classes.

First we need a `Matcher` which is the main abstraction of the assertions library. Following the documentation we know:

> Implementations contain a single function, called 'test', which accepts a value of type T and returns an instance of MatcherResult. This MatcherResult return value contains the state of the assertion after it has been evaluated.

To create an instance of `MatcherResult` one needs:
- a `Boolean` indicating whether the test has passed or not
- a `() -> String` function returning a message that should be returned in case of a failure
- a `() -> String` function returning a message that should be returned in case of a failure, used in case of negation

```kotlin
fun <A, B> beRight(check: (B) -> Boolean): Matcher<Either<A, B>> =
    Matcher { tested ->
        MatcherResult(
            passed = tested.fold(
                left = { false },
                right = { check(it) },
            ),
            failureMessageFn = {
                tested.fold(
                    left = { "Either had an unexpected Left value of $it" },
                    right = { "Either had an unexpected Right value of $it" }
                )
            },
            negatedFailureMessageFn = {
                tested.fold(
                    left = { 
                        // what should be returned for this case?
                        // if this matcher is negated and we do not want
                        // a Right but we get a Left then the test will
                        // pass
                        "" 
                    },
                    right = { "Either should not have Right value of $it" }
                )
            },
        )
    }
```

Creating a function that returns a `Matcher` is not enough, we should conform to the library style and create a proper infix function.

```kotlin
infix fun <A, B> Either<A, B>.shouldBeRight(check: (B) -> Boolean): Either<A, B> {
    this should beRight(check)
    return this
}
```

Through this the newly created assertion can be simply used.

```kotlin
class SomeTest : BehaviorSpec({
    given("a new tested class") {
        val expected = ...
        val tested = TestedClass()

        `when`("doing things") {
            val actual: Either<Failure, Foo> = tested.doThings()

            then("the foo should have 42 and be important") {
                actual shouldBeRight {
                    it == Foo(42) && it.isSomethingImportant()
                }
            }
        }
    }
})
```

Maybe the name `shouldBeRight` should be better (sic!) but other then that everything looks nice and should make testing easier.