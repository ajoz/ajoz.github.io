---
layout: post
date: 01-07-2014
author: Andrzej Jóźwiak
title: Confitura 2017 afterthoughts
tags: confitura conference java scala jvm
disqus: true
---

This years [Confitura](https://2017.confitura.pl/schedule) was a tough nut to crack, there was a lot of talks I wanted to attend and I really had to choose carefully. The worst thing is that the conference returned to it's old location which ment no air conditioning and going a lot down and forth the stairs other then that everything was fine. Now without further ado:

### Why TDD is slowing you down? - Adam Pierzchała, Wojciech Przechodzeń

I was torn between this talk and talk from a well known speaker Jakub Nabrdalik. In the end I chose TDD as I thought that Jakub will talk more about architecture of enterprise apps. I was wrong, I saw Jakub's talk a bit later online and I regret my choice now. The first thing I noticed is that this year's talks have information about complexity level. TDD talk was marked as advanced so I hoped for the best but was disapointed.

Everything started with a usual reference to *Red-Green-Refactor*, then a bit about **FIRST** testing principle:
* *F - FAST*
* *I - ISOLATED*
* *R - REPEATABLE*
* *S - SELF-VERYFYING*
* *T - TIMELY*

I'm very interested in TDD as a technique, this is why I always try to discuss a question that is bothering me for some time: *Does Red-Green-Refactor always lead to a correct solution?* I define correctness not only by the fact that the tests are passing. I'm really interested if there was any research about it, like comparing solutions that were designed first and solutions that came to existance through TDD. I would love to read about the comparison of code complexity, amount of bugs, eventual maintainence etc. But let's get back to the topic.

Guys were explaining usual TDD calisthenics: readability, what can be treated as a single unit, size of tests. I can't say it wasn't well delivered or entertaining but it was hardly an "advanced" material. Also after reading the title I expected this talk to be more about issues with TDD, what things about us slow us down, what kind of habits can hinder good TDD.

Still there was an interesting thing mentioned about SQLite project. It has 745 times more test code than production code. WoW! Sounds impressive but is it so unbelivable?

### A Hitchhiker's Guide to the Functional Exception Handling in Java - Grzegorz Piwowarek

Some time ago Grzegorz gave a nice talk about functional programming for JUG Łódź, I've also seen few of his talks online (thank you youtube!) so the choice seemed simple. I was again a bit disapointed. Grzegorz was jumping through language and library examples: `Java 8` -> `Scala` -> `Vavr.io` imho a bit confusing for an average listener.
