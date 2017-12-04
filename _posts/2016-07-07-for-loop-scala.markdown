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

Let's try to answer these questions and few more along the way!

Below is this "groundbreaking" (at least for me) for-loop example:

```scala
for(i <- 0 until args.length) {
    println(args(i))
}
```

I've started from thinking about the notation, is `for` just a function? If it is a function then what type of an argument does it accept? I knew already that what other languages call operators, in Scala are just plain functions (methods) of a given type (class).

For anyone with only a **Java** background this might look strange, but `1 + 2` is equivalent to `1.+(2)`. [Infix notation](http://docs.scala-lang.org/style/method-invocation.html) allows to remove a lot of clutter from the code. It can also create a lot of confusion but that's a topic for another story.

Without access to a **Scala** REPL or a compiler I visualized how this might look with a "dot" notation:

```scala
for(i.<-(0.until(args.length))) {
    println(args(i))
}
```

If it's working for `+` or `-` than it surely has to work for a strange back arrow like `<-`, right? But how on bloody earth this magical `i` works?.

To move this small investigation forward I stopped bothering myself with `i`. Without a REPL or a compiler, the easiest way to check was to look into Scala code on [github](https://github.com/scala/scala). But what should I look for? I imagined it to be something like:

```scala
// Somewhere in type Int or RichInt:
def <-(x: Range): ??
```

But what should be the return type? First I though that it's probably a `Range` which would mean that `for` is a function that looks like:

```scala
def for(x: Range): Unit
```

No luck here. Then I considered a `Seq`:

```scala
def for(x: Seq): Unit
```

Also no luck and what's even worse I couldn't find `<-` implementation anywhere. Those fruitless deliberations were ended with a more sophisticated `for` loop example in Scala:

```scala
for(i <- 0 until 100 by 3; if somecondition; j <- 0 until 100 by 3) {
    // something done here
}
```
Such powerfull example made me think that `<-` is really not an operator one can implement. Which in turn explained why I couldn't find its implementation in `Int` or `RichInt`. It's the end of the road, a look into [docs](http://docs.scala-lang.org/tutorials/FAQ/finding-symbols.html) is necessary.

All operators in Scala can be divided into four categories:
- reserved symbols (keywords) - those are not implemented as methods of any type and cannot be used as names
- methods or values
- methods that a type has access to thanks to implicit conversion
- syntactic sugar

The mysterious `<-` is a special keyword, that cannot be used outside of `for`. The plot thickens!

What about `until` and `by`? This one is simple. Method `until` can be found in [RichInt](https://github.com/scala/scala/blob/2.12.x/src/library/scala/runtime/RichInt.scala):

```scala
/**
  * @param end The final bound of the range to make.
  * @return A [[scala.collection.immutable.Range]] from `this` up to but
  *         not including `end`.
  */
def until(end: Int): Range = Range(self, end)

/**
  * @param end The final bound of the range to make.
  * @param step The number to increase by for each step of the range.
  * @return A [[scala.collection.immutable.Range]] from `this` up to but
  *         not including `end`.
  */
def until(end: Int, step: Int): Range = Range(self, end, step)
```

Looking at the code it's easy to deduce that `by` is a method of [Range](https://github.com/scala/scala/blob/2.12.x/src/library/scala/collection/immutable/Range.scala):

```scala
/** Create a new range with the `start` and `end` values of this range and
 *  a new `step`.
 *
 *  @return a new range with a different step
 */
def by(step: Int): Range = copy(start, end, step)
```

Ok to piece what we have so far. In the original code example:

```scala
for(i <- 0 until args.length) {
    println(args(i))
}
```

- `O` is implicitly converted from `Int` to `RichInt`
- `until` is invoked on `RichInt` and returns a `Range`
- we get `for(i <- Range)`, but how does it work?

Certainly Scala Language Specification will [help](http://scala-lang.org/files/archive/spec/2.11/06-expressions.html#for-comprehensions-and-for-loops). In the chapter *6.19 For Comprehensions and For Loops* I found a nice and concise definition:

```
Expr1          ::=  `for' (`(' Enumerators `)' | `{' Enumerators `}')
                       {nl} [`yield'] Expr
Enumerators    ::=  Generator {semi Generator}
Generator      ::=  Pattern1 `<-' Expr {[semi] Guard | semi Pattern1 `=' Expr}
Guard          ::=  `if' PostfixExpr
```

Finally mysterious `<-` operator is found, still this needs a bit of deciphering. Scala syntax is defined with [Extended BNF](https://en.wikipedia.org/wiki/Extended_Backus%E2%80%93Naur_form) and even without expert knowledge of BNF it's possible to understand that:
- `for` is an expression that can have two forms
  - with parentheses `for( ... )`
  - with braces `for { ... }`
- in both of these forms `Enumerators` sequence always starts with a `Generator`, optionally followed by further generators
- each `Generator` is composed from a pattern matching (written as `Pattern1 <- Expr`) and a series of optional `Guards` and value definitions.

This explains the syntax but how does it work? As always documentation comes to the rescue:

> The precise meaning of generators and guards is defined by translation to invocations of four methods: map, withFilter, flatMap, and foreach. These methods can be implemented in different ways for different carrier types.

I can confirm this with some decompiler magic. I used `javap -v -p` to get verbose output and some detailed information about all classes and members.

```
10: aload_1
11: arraylength
12: invokevirtual #35                 // Method scala/runtime/RichInt$.until$extension0:(II)Lscala/collection/immutable/Range;
15: aload_1
16: invokedynamic #57,  0             // InvokeDynamic #0:apply$mcVI$sp:([C)Lscala/runtime/java8/JFunction1$mcVI$sp;
21: invokevirtual #63                 // Method scala/collection/immutable/Range.foreach$mVc$sp:(Lscala/Function1;)V
24: return
```

Once again documentation was correct -- who would have known? I wanted to double check and see how guards are compiled, so I've modified the example a bit:

```scala
// args is a char array
for(i <- args; if i == 'a') {
    println(i)
}
```

And the decompiled code:

```
// the for:

7: aload_1
8: invokevirtual #31                 // Method scala/Predef$.charArrayOps:([C)[C
11: invokespecial #34                 // Method scala/collection/mutable/ArrayOps$ofChar."<init>":([C)V
14: invokedynamic #55,  0             // InvokeDynamic #0:apply:()Lscala/Function1;
19: invokevirtual #59                 // Method scala/collection/mutable/ArrayOps$ofChar.withFilter:(Lscala/Function1;)Lscala/collection/generic/FilterMonadic;
22: invokedynamic #64,  0             // InvokeDynamic #1:apply:()Lscala/Function1;
27: invokeinterface #70,  2           // InterfaceMethod scala/collection/generic/FilterMonadic.foreach:(Lscala/Function1;)V
32: return

// the guard (if == 'a'):

0: iload_0
1: bipush        97 // 'a'
3: if_icmpne     10
6: iconst_1
7: goto          11
10: iconst_0
11: ireturn
```

The defined guard was changed into a `withFilter` method invocation. Documentation was right yet again, darn it! The second example can be written as:

```scala
args
  .withFilter(i => i == 'a')
    .foreach(i => println(i))
```

WoW! I need to say that this exceeded my wildest expectations. Comming from a Java background I never knew such powerfull and concise tool. It seems that it's worth to get your hands dirty with a decompiler sometimes. 
