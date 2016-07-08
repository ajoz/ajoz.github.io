---
layout: post
date: 07-07-2016
author: Andrzej Jóźwiak
title: Adventures in implementing "for" loop
tags: fp functional scala java jvm
disqus: true
---

I was reading "Seven Languages in Seven Weeks" from the "The Pragmatic Bookshelf"  series by Bruce A. Tate when I found chapter about Scala. The language is nothing new to me, I'm even trying to sharpen my understanding of functional programming concepts by reimplementing Scala features in Scala / Java. What peeked my interest was an example of a simple for loop. I couldn't continue reading as my thoughts rushed and I begun to think how it is implemented internally.

A simple for-loop example:

```scala
for(i <- 0 until args.length) {
    println(args(i))
}
```

I started to think about such notation in terms of what is a higher order function and what are simple arguments, operators maybe. From my small knowledge of Scala capabilities I know that some of the available operators are just functions (methods) in given types (classes). So my first thought about above example was to write it more expressively:

```scala
for(i.<-(0.until(args.length))) {
    println(args(i))
}
```

I'm used to thinking about operators like `+` or `-` in terms of methods in Scala so I thought the same was valid for `<-`. I've begin to think how would such a function work and how on bloody earth I would be able to iterate with this magical `i` So I've begun looking for it. I thought the easiest way is to look into Scala code on github and just check this `<-` operator implementation. I imagined it to look like:

```scala
def <-(x: Range): ??
```

And here everything breaks as I couldn't imagine what type should the `<-` function return. First I though that its maybe a `Range` which would mean that `for` is a function that looks like:

```scala
def for(x: Range): Unit
```

Or maybe a `Seq`:

```scala
def for(x: Seq): Unit
```

But then I saw what power for-loop in Scala has:

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
