---
layout: post
date: 11-12-2017
author: Andrzej Jóźwiak
title: The more constraints the better?
tags: kotlin fp conventions coderetreat
disqus: true
---

It's been almost a month since the [Coderetreat](http://ajoz.github.io/2017/11/19/coderetreat-lodz-2017/) in Łódź organized by local  [JUG](https://www.meetup.com/Java-User-Group-Lodz/). It was a great opprotunity to learn and hone skills with a simple and approachable problem. Coderetreat is most often about Object Oriented programming and Test Driven Development thus most sessions revolve around [Object Calisthenics](http://williamdurand.fr/2013/06/03/object-calisthenics/). Still I wanted to try out something different, and implement the [Game of Life](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life) with a Functional Programming approach.

FP ment using completely different set of constraints. I decided upon these:
- no unnamed lambdas (no matter how tempting they are)
- no mutable state
- no explicit loops (for, while)
- state decoupled from the behaviour (no OO)
- side effects at the boundries of the "system" (pure functions should not call impure code)
- expressions used instead of statements
- one argument functions (this ment currying and partial application)
- point-free notation wherever possible

There is a nice article summing up and explaining all of the Functional Programming [Calisthenics](https://codurance.com/2017/10/12/functional-calisthenics/#oneargumentfunctions), I advise you to read it.

I have also decided to make some constraints about the game representation:
- map can be infinite in size
- only alive cells exist in the world (no separation into a Live and Dead cell)

This sounds like a lot of constraints. Where to begin?

Most people begin with defining a `Cell` class, I started with asking myself how will the game work? I want to move the side effects to the boundry of the system. What does this mean? Simply getting some user input (if any), getting the game output on the screen and controlling the game iterations. It should be possible to pass some game state and get back next game state. Such function should be pure and easily testable.

We can start with the said function, it should take some game state and return game state:

```kotlin
fun nextGameState(gameState: State): State {
  // something here
}
```

This immediately forces us to define a `State`, but we do not yet know what it is and how should it behave. Here comes a really nice Kotlin feature, a `typealias`. This is very, very handy feature. It allows us to define different names for existing types. What's so handy about it? It greatly enhances readability and helps with lowering the cognitive context switching. I think it also allows to focus on the subject matter rather then the implementation details.

Types are handy but often can poison our thinking about a subject. Poison? Yes, poison in a sense that we will start to define things in terms of types we use. It doesn't sound so bad, but I prefere to read code like:

```kotlin
fun nextGameState(gameState: State): State {}
```

instead of the code:

```kotlin
fun nextGameState(gameState: List<Pair<Int, Int>>): List<Pair<Int, Int>> {}
```

At least for me, it's immediately distracting and leaks the details of the internal implementation. 

// names heave meaning! names bring meaning to the code
// for the algorithm to be readable, names need several iterations
// we read more code then we write
// we need to perform the same procedures for code as we do for writing books
// reading a text several times helps to check if the meaning is still there
// if the reading flow is there
// for better reading the same things need different names, to show and express intent
