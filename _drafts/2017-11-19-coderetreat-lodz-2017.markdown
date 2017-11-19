---
layout: post
date: 19-11-2017
author: Andrzej Jóźwiak
title: Global Day of Coderetreat Łódź 2017 afterthoughts
tags: 4developers conference java jvm agile
disqus: true
---

Each year [JUG Łódź](https://www.meetup.com/Java-User-Group-Lodz/) is hosting (with ofc ;-) the great help of our sponsors) a Global Day of Coderetreat. For anyone that is not familiar with this type of event: it's a whole day of 45 minute sessions of pair programming. After each session there is retrospective where each pair describes what went well and what went wrong. Did I mention that we randomize people in pairs each time? Yes! Coderetreat is supposed to help you start looking on a problem from a different angle: if your partner is using different language, platform, technology than you're used to it's a wonderful opportunity to learn.

We started with introducing ourselves, I laughed a bit because it looked like an AA meeting ;-) Every participant stood up and said his name and programming languages he had configured on his laptop.

You can imagine that on an event prepared by JUG there will be a lot of Java, but many people wanted to work in Scala, Kotlin. We had one person with C++, one with Erlang and one with F# (me). When people started to describe what they wanted to try the most Kotlin was the definite winner here. As I normally work in Java / Kotlin I hoped for a session with someone programming in Haskell, Lisp, ML or any less mainstream language - maybe next time.

Before I start describing each session and how they went, I would like to quickly thank the facilitators of the event: [Paweł Włodarski](http://pawelwlodarski.blogspot.com/) and Piotr Przybylak. They did a really great job. They introduced each session rules and explained the issues we might find. During the sessions they went from pair to pair to monitor the progress and answer any questions that poped up. Great work guys!

### The Problem - John Conway's Game of Life

The "Game" plays out on an infinite two-dimensional grid of cells, each of which can be dead or alive. Every cell has eight neighbours, each step of the game the following rules are used:
* Live cell with fewer then two neighbours dies (underpopulation)
* Live cell with two or three neighbours stays alive
* Live cell with four or more neighbours dies (overpopulation)
* Dead cell with three neighbours becomes alive (reproduction)

To start the game you need to pass a initial pattern (seed) of alive and dead cells. The game continues infinitely (or for how long you want). You can read more about it on [wikipedia](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life).

### Session 1 - No limits!

First session was a warmup, we could code however we wanted, no limits. I already had few ideas how to approach the problem the week before the event so I really wanted to hear how other people think. After a little bit discussion with my partner, we came came up to a solution like this:
* there is a game board of size: width * height
* the game board represents all the cells dead and alive
* we will scan the board with a 3x3 window and check each cells neighbourhood

I suggested that we should go with a stateless solution. So the `Game` just takes `Board` state and produces another `Board` state. After a bit of implementing, Paweł came and told us:
* we should have an infinite board (and we already limited ourselves with an array)
* we should write tests (we did not)
* we should try to focus on the smallest part of the problem

After those suggestions we went on testing and implementing just the rules of the "Game".

### Retrospective 1

* 45 minutes is not a lot
* people have different ideas and it's hard to understand each other in a short amount of time
* no tests is a problem, also correctly determining what you should start to test is
* many people didn't finish just like us

### Session 2 - 
