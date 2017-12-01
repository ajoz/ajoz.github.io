---
layout: post
date: 07-07-2016
author: Andrzej Jóźwiak
title: A "for" loop adventure in Scala
tags: fp functional scala jvm
disqus: true
---

I was reading *"Seven Languages in Seven Weeks"* from the *"The Pragmatic Bookshelf"* series by Bruce A. Tate when I found chapter about **Scala**. What peeked my interest was an example of a simple and harmless `for` loop. I just couldn't continue reading as my thoughts rushed and I begun to think how it's implemented internally.

* Is `for` just a higher-order function?
* Is `<-` an infix function that works like an operator?
* Is `until` an extension for the type `Int`?

Let's try to answer those questions and few more along the way!

Below is this "groundbreaking" (at least for me) for-loop example:

```scala
for(i <- 0 until args.length) {
    println(args(i))
}
```

I started from thinking about the notation, is `for` just a function? If it is a function then what type of an argument does it accept? I knew already that what other languages call operators, in Scala are just plain functions (methods) of a given type (class).

For anyone with only a **Java** background this can be strange, but `1 + 2` is equivalent to `1.+(2)`. This [infix notation](http://docs.scala-lang.org/style/method-invocation.html) allows to remove a lot of clutter from the code. It can also create a lot of confusion if we allow our imagination to run wild but that's a topic for another story.

Without access to a **Scala** REPL or a compiler I visualized how this might look with a "dot" notation:

```scala
for(i.<-(0.until(args.length))) {
    println(args(i))
}
```

If it's working for `+` or `-` than it surely has to work for `<-`, right?

How would such a function work and how on bloody earth this magical `i` works?. Best is not to bother self with `i` as this will surely resolve once I understand how `<-` works, Without a REPL or a compiler, the easiest way to check was to look into Scala code on [github](https://github.com/scala/scala) and just check this `<-` operator implementation. What should I look for? I imagined it to be something like:

```scala
def <-(x: Range): ??
```

But what is the return type? First I though that it's probably a `Range` which would mean that `for` is a function that looks like:

```scala
def for(x: Range): Unit
```

Or maybe a `Seq`:

```scala
def for(x: Seq): Unit
```

But then I saw the real power of a for-loop in Scala:

```scala
for(i <- 0 until 100 by 3; if somecondition; j <- 0 until 100 by 3) {
    // something done here
}
```

This made me think is `<-` really an operator implemented in `Int` or `RichInt`?


Materials:

How to find what some symbols means:

http://docs.scala-lang.org/tutorials/FAQ/finding-symbols.html

Index of all the keywords / operators in scala:

http://www.artima.com/pins1ed/book-index.html#indexanchor

For expression in scala documentation:

http://www.artima.com/pins1ed/builtin-control-structures.html#sec:for-expressions

For comprehension:

http://www.artima.com/pins1ed/for-expressions-revisited.html

Valid identifiers in scala:

http://stackoverflow.com/questions/7656937/valid-identifier-characters-in-scala

Symbolic operators in scala:

http://stackoverflow.com/questions/7888944/what-do-all-of-scalas-symbolic-operators-mean

http://docs.scala-lang.org/tutorials/FAQ/yield.html

Where until / to is implemented:
https://github.com/scala/scala/blob/2.12.x/src/library/scala/runtime/RichInt.scala
https://github.com/scala/scala/blob/2.12.x/src/library/scala/Int.scala

Where by method is implemented:

https://github.com/scala/scala/blob/2.12.x/src/library/scala/collection/immutable/Range.scala
