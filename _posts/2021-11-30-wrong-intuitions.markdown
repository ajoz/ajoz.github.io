---
layout: post
date: 30-11-2021
author: Andrzej Jóźwiak
title: When your intuitions are wrong
tags: kotlin jvm mocking testing tests mocks mockk mockito
disqus: true
---

Recently I was asked by one of my colleagues for help with some failing unit tests. On the surface level it did not seem very complex, it seemed just like a simple copy paste mistake. [Mocking library](https://mockk.io/) verification was reporting that a method was not called.

```
Verification failed: call 1 of 1: Foo(#1).removeListener(eq(foo_1_key), eq(io.github.ajoz.capturing.Bar$foo3Listener$1@38f4800d))). No matching calls found.

Calls to same method:
1) Foo(#1).removeListener(foo_1_key, io.github.ajoz.capturing.Bar$foo1Listener$1@59173248)
2) Foo(#1).removeListener(foo_2_key, io.github.ajoz.capturing.Bar$foo2Listener$1@45cecf30)
3) Foo(#1).removeListener(foo_3_key, io.github.ajoz.capturing.Bar$foo3Listener$1@38f4800d)
```

First I wanted to confirm that this is not a copy paste error. I went through the places where the `removeListener` was called by the tested class, but everything seemed fine. It was obvious from the stacktrace that an expected listener was not registered, but the code seemed fine. There was nothing complicated with it, no additional ways to call the methods in a wrong order or ommit calling them.

```kotlin
class Bar(
    private val foo: Foo
) {
    private val foo1Listener = FooStateListener {
        println("Foo 1 Listener")
    }

    private val foo2Listener = FooStateListener {
        println("Foo 2 Listener")
    }

    private val foo3Listener = FooStateListener {
        println("Foo 3 Listener")
    }

    fun start() {
        foo.addListener(FOO_1_KEY, foo1Listener)
        foo.addListener(FOO_2_KEY, foo2Listener)
        foo.addListener(FOO_3_KEY, foo3Listener)
    }

    // other methods and business logic ...

    fun stop() {
        foo.removeListener(FOO_1_KEY, foo1Listener)
        foo.removeListener(FOO_2_KEY, foo2Listener)
        foo.removeListener(FOO_3_KEY, foo3Listener)
    }
}
```

As I did not suspect the test code at that time, I carefully rechecked the tested class once more. Nothing surprising, the listeners were added in the `start` method then removed in `stop`. I started looking at the test code, but similarly to the tested class, it looked fine.

```kotlin
internal class MockKBarTest {
    @Test
    fun `Check if listener is added and removed`() {
        // given:
        val foo = mockk<Foo>()
        val bar = Bar(foo)

        every { foo.removeListener(any(), any()) } answers {}
        every { foo.addListener(any(), any()) } answers {}

        val foo1Slot = slot<FooStateListener>()
        val foo2Slot = slot<FooStateListener>()
        val foo3Slot = slot<FooStateListener>()

        // when:
        bar.start()
        bar.stop()

        // then:
        verify { foo.addListener(key = FOO_1_KEY, listener = capture(foo1Slot)) }
        verify { foo.addListener(key = FOO_2_KEY, listener = capture(foo2Slot)) }
        verify { foo.addListener(key = FOO_3_KEY, listener = capture(foo3Slot)) }

        verify { foo.removeListener(key = eq(FOO_1_KEY), listener = foo1Slot.captured) }
        verify { foo.removeListener(key = eq(FOO_2_KEY), listener = foo2Slot.captured) }
        verify { foo.removeListener(key = eq(FOO_3_KEY), listener = foo3Slot.captured) }
    }
}
```

First I decided to simplify the test and just randomly commented out some of test code. Just by chance I left `FOO_3_KEY` intact and removed the other ones.

```kotlin
        // then:
//        verify { foo.addListener(key = FOO_1_KEY, listener = capture(foo1Slot)) }
//        verify { foo.addListener(key = FOO_2_KEY, listener = capture(foo2Slot)) }
        verify { foo.addListener(key = FOO_3_KEY, listener = capture(foo3Slot)) }

//        verify { foo.removeListener(key = eq(FOO_1_KEY), listener = foo1Slot.captured) }
//        verify { foo.removeListener(key = eq(FOO_2_KEY), listener = foo2Slot.captured) }
        verify { foo.removeListener(key = eq(FOO_3_KEY), listener = foo3Slot.captured) }
```

To my surprise the tests passed. 

```
BUILD SUCCESSFUL in 2s
3 actionable tasks: 2 executed, 1 up-to-date
11:38:09 PM: Task execution finished ':test --tests "io.github.ajoz.capturing.MockKBarTest.Check if listener is added and removed"'.
```

Although strange and surprising there was no other choise but to uncomment `FOO_2_KEY` and check what will happen. There was no surprise anymore because the test failed.

```
Verification failed: call 1 of 1: Foo(#1).removeListener(eq(foo_2_key), eq(io.github.ajoz.capturing.Bar$foo3Listener$1@632fd018))). No matching calls found.

Calls to same method:
1) Foo(#1).removeListener(foo_1_key, io.github.ajoz.capturing.Bar$foo1Listener$1@7908eb07)
2) Foo(#1).removeListener(foo_2_key, io.github.ajoz.capturing.Bar$foo2Listener$1@6f1e7b69)
3) Foo(#1).removeListener(foo_3_key, io.github.ajoz.capturing.Bar$foo3Listener$1@632fd018)
```

I could not brute force this issue, I had to start reading with understanding. It looks that `foo2Slot.captured` contains a `foo3Listener`. It seemed like a very strange thing as I was capturing only `foo_2_key` in that slot. Just looking at this did not solve the issue, so it ment I had to look and Mockk code.

Inside the `UnorderedCallVerifier` in the `matchCall` I found a very descriptive explanation that should be an error.

```kotlin
if(allCallsForMockMethod.size > 1 && matcher.args.any { it is CapturingSlotMatcher<*> }) {
    val msg = "$matcher execution is being verified more than once and its arguments are being captured with a slot.\n" +
        "This will store only the argument of the last invocation in the slot.\n" +
        "If you want to store all the arguments, use a mutableList to capture arguments."
    throw MockKException(msg)
}
```

This led me to the Mockk [issue #352](https://github.com/mockk/mockk/issues/352), which more or less described my problem. The solution was to use a list instead of a slot. The descriptive error message is available from version 1.12.0. Although there was nothing more to find out I started to think if my expectations for how the slot could work were wrong. Is it so wild to expect that multiple slots, for differently matched calls should contain different values? Is it a wrong intuition? Where did I get it from?

I thought about rewriting the same test with [Mockito](https://github.com/mockito/mockito-kotlin/) and checking if its `ArgumentCaptors` work like `slots` from Mockk. I recreated the same structure of the tests. All `foo*Slots` became `foo*Captors`, other then simple name and invocation changes, there was nothing out of the ordinary.

```kotlin
internal class MockitoBarTest {
    @Test
    fun `Check if listener is added and removed`() {
        // given:
        val foo = mock<Foo>()
        val tested = Bar(foo)

        val foo1Captor = argumentCaptor<FooStateListener>()
        val foo2Captor = argumentCaptor<FooStateListener>()
        val foo3Captor = argumentCaptor<FooStateListener>()
        
        // when:
        tested.start()

        verify(foo).addListener(eq(FOO_1_KEY), foo1Captor.capture())
        verify(foo).addListener(eq(FOO_2_KEY), foo2Captor.capture())
        verify(foo).addListener(eq(FOO_3_KEY), foo3Captor.capture())

        tested.stop()

        // then:
        verify(foo).removeListener(eq(FOO_1_KEY), eq(foo1Captor.firstValue))
        verify(foo).removeListener(eq(FOO_2_KEY), eq(foo2Captor.firstValue))
        verify(foo).removeListener(eq(FOO_3_KEY), eq(foo3Captor.firstValue))
    }
```

With persistence worthy of a better cause I awaited the results. 

```
BUILD SUCCESSFUL in 1s
3 actionable tasks: 2 executed, 1 up-to-date
12:11:13 AM: Task execution finished ':test --tests "io.github.ajoz.capturing.MockitoBarTest.Check if listener is added and removed"'.
```

Ok. Success. At least I confirmed that it is not outlandish to expect argument capturing to work like this but this really made me think how hard is to write good tools. One needs to know other existing solutions and what intuitions will the users bring from them. I scanned the Mockk tutorials in hopes of proving that I cannot read with understanding (which would not be that hard) but I could not find anything about such peculiar slot behaviour.

So everything is fine as long there is a big explanation in the tutorial? Not necessarily, because to be completely frank I don't know how long I will remember about it. I don't know how long anyone will remember about this. This is a very out of ordinary behaviour which shows just how much magic is done behind a thin veil of a familiar looking API. Maybe API can look too familiar for its own good? As a tool designer one should avoid fighting with intuitions as this will lead to lots of confusion.