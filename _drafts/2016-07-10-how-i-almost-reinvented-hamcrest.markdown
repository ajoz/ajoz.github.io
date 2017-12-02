---
layout: post
date: 01-07-2016
author: Andrzej Jóźwiak
title: How I almost reinvented Hamcrest
tags: java hamcrest junit
disqus: true
---

I started using Scala and I got really spoiled with it. Both language wise and test wise. I always wanted to read my code and tests like sentences like real human language. Language without all those brackets, dots, parenthesis etc.

Lets say you have a unit test code and you write in it:
```
{
Assert that session contains command mute
}
```

It reads phenomenally and quickly shows the intentions of the author of this test. Of course in `Java` it would look like:

```java
{
Assert.that(session).contains(Command.MUTE);
}
```

Still it is quite nice.

Work In Progress!

- how my "reinvent the wheel" concept looked like:

Assertion like sentence ---- Assert that {object} contains {object}*

Sentence can be simple or complex. Complex sentences are built with: and, or

Assert Sentence #1 {or | and} Assert Sentence #2 {or | and} Assert Sentence #N

For complex testing assertions

So how to do it in Hamcrest


- Hamcrest
- Custom Matchers
- Hamcrest looses readibility
- how to enhance hamcrest in pure java / android
- how scala / groovy spoils programmers with language features
- how to ease the pain on Android

http://docs.mockito.googlecode.com/hg/org/mockito/Matchers.html
http://www.hascode.com/2012/10/make-your-tests-more-readable-with-custom-hamcrest-matchers/
http://joewalnes.com/2007/07/20/creative-uses-of-hamcrest-matchers/

- FEST Fluent Assertions
