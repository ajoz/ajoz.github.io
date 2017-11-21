---
layout: post
date: 19-11-2017
author: Andrzej Jóźwiak
title: Global Day of Coderetreat Łódź 2017
tags: coderetreat jug
disqus: true
---

Each year [JUG Łódź](https://www.meetup.com/Java-User-Group-Lodz/) is hosting (with ofc the great help of our sponsors) a [Global Day of Coderetreat](http://coderetreat.org/). For anyone that is not familiar with this type of event: it's a whole day of 45 minute sessions of pair programming. After each session there is retrospective where each pair describes what went well and what went wrong. Did I mention that we randomize people in pairs each time? Yes! Coderetreat is supposed to help you start looking on a problem from a different angle: if your partner is using different language, platform, technology than you're used to it's a wonderful opportunity to learn.

We started with introducing ourselves, I laughed a bit because it looked like an AA meeting. Every participant stood up and said his name and programming languages he had configured on his laptop.

You can imagine that on an event prepared by JUG there will be a lot of **Java**, but many people wanted to work in **Scala**, **Kotlin**. We had one person with **C++**, one with **Erlang** and one with **F#** (me). When people started to describe what they wanted to try the most then Kotlin was the definite winner. As I normally work in Java / Kotlin I hoped for a session with someone programming in Haskell, Lisp, ML - maybe next time.

Before I start describing each session and how they went, I would like to quickly thank the facilitators of the event: [Paweł Włodarski](http://pawelwlodarski.blogspot.com/) and Piotr Przybylak. They introduced each session rules and explained issues we might find. During the sessions they went from pair to pair to monitor the progress and answer any questions that poped up. Great work guys!

### The Problem - John Conway's Game of Life

The "Game" plays out on an infinite two-dimensional grid of cells, each of which can be dead or alive. Every cell has eight neighbours, each step of the game the following rules are used:
* Live cell with fewer then two neighbours dies (underpopulation)
* Live cell with two or three neighbours stays alive
* Live cell with four or more neighbours dies (overpopulation)
* Dead cell with three neighbours becomes alive (reproduction)

To start the game you need to pass a initial pattern (seed) of alive and dead cells. The game continues infinitely (or for how long you want). You can read more about it on [wikipedia](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life).

### Session 1 - No limits!

First session was a warmup, we could code however we wanted, no limits. I already had few ideas how to approach the problem the week before the event so I really wanted to hear how other people think. After a little bit of a discussion with my partner, we came up with a solution like this:
* there is a game board of size: `width * height`
* the game board represents all the cells dead and alive
* we will scan the board with a 3x3 window and check each cells neighbourhood
* very similar to image processing where you scan the image and change a pixel depending on its neighbourhood
* we will use Java 8

I suggested that we should go with a stateless solution. So the `Game` just took `Board` state and produced another `Board` state. After a bit of implementing, Paweł came and told us:
* we should have an infinite board (and we already limited ourselves with an array)
* we should write tests (we did not)
* we should try to focus on the smallest part of the problem (like dead / alive rules)

After this recommendation we went on to testing and implementing just the rules of the "Game". Unfortunately we just managed to write few tests before we ran out of time.

### Retrospective 1

* 45 minutes is not a lot of time
* people have different ideas and it's hard to understand each other in a short amount of time
* having no tests is a problem, also correctly determining what you should start to test
* many people didn't finish just like us

### Session 2 - No ifs, methods not longer then 5 lines

I need to appologize to my second partner because I completly dominated that session. I just wanted to implement one of my ideas to solve the problem so badly :-( Sorry Marek! I tweaked the limits for this session and added some more:
* no mutable state
* no evident iteration like `while` and `for` loops
* functions should be simple and [referential transparent](https://en.wikipedia.org/wiki/Referential_transparency)
* solution should be as short as possible without loosing readability (proper and descriptive names, indents etc)
* everything done in Kotlin

So what devious idea did I have? It's very simple or at least I thought so before I saw my partner's face expression ;-) Bascially everyone so far was implementing the "Game" with a distinction between alive and dead cells. Why not just drop the idea of dead cells completly? I know it's tempting to use those to ease the "reproduction" part of the game but do we really need those?

My solution:
* only live cells exist!
* we calculate neighbours of each live cells (those are bascially candidate cells)
* we combine those neighbour lists into one
* we check how many times a certain neighbour appears on the list
* we can now simplify rule checking to:
  * if the candidate appears 3 times (has 3 live neighbours) then it will become a live cell in next iteration of the game
  * if the candidate appears 2 times (has 2 live neighbours) and is already a live cell it survices to the next iteration

Altough Kotlin is not a pure functional programming language it was a pure pleasure (pun intended!) to implement the whole thing. Especially collections made everything easy as walk in the park. It was possible to focus only on the algorithm! Our final solution was more or less 50 lines long (sic!). By final solution I mean the code without printing game iterations or running it.

### Retrospective 2

* it's easy to dominate someone (I need to learn avoiding it in the future)
* it was a good idea to drop the alive / dead cell distinction when representing a single cell
* it wasn't hard to code everything without ifs
* Paweł and Piotr explored the idea of expressing the particular states of cells as a class hierarchy. Instead of having a cell class with a neighbour count field we could have a class hierarchy with `CellWith1Neighbour`, `CellWith2Neighbours`, `CellWith3Neighbours`, `InvalidCell` etc. Interesting!
* I leared I love Kotlin's `typealias` a lot
* again we had no tests before implementation :-(

### Session 3 - No nested ifs and loops, single primitive type per class, no type aliases (oops!)

After short discussion we went with implementing the `Cell` class hierarchy idea. My partner wanted to learn Kotlin better so I thought this session would be nice to introduce him to some concepts like: `sealed class`, `data class`, `extension function` and `extension fields`. I was happy that I could teach something in this short amount of time. We had also a short but nice discussion about the Observer pattern implementation, I was advised to watch this [talk](https://www.youtube.com/watch?v=FM1JQkJWAZA) by Adam Badura and I definitely will!

You probably noticed that I'm hardly writing anything about our glorious implementation? You're correct because it didn't went well. We created the hierarchy but we had issues with traversing cells correctly. Not a lot of conclusions on the retrespective so I will skip it.

### Session 4 - Using mainly Streams / Sequences, no lambdas, only named functions

I loved this session! It was a real mindbender and a tough one. Everything seemed simple until we started thinking what should be produced by the stream.
* Should it be a stream of single cells?
* Or maybe whole generations of cells? (this would mean we would stream whole "Game" iterations)

I have only small experience with streams (mostly RxJava), in most cases they were backed up by finite collections. Belive me I did not know where to start. I had a vision of combining or zipping the streams, but what streams I did not know ;-) As always Paweł came with help and suggested combining stream of live cells with a stream of neighbours but we were lost in the implementation.

### Retrospective 4

* we did not finish the implementation (again)
* still it was a lot of fun (best session so far for me)
* all this talking about streams reminded me about a very nice talk by [Kevlin Henny](https://www.youtube.com/watch?v=nrVIlhtoE3Y), where he showed how to implement **FizzBuzz** in Haskell using infinite streams of values. I wonder can the Game of Life be solved in similar manner?

### Session 5 - TDD As If You Meant It!

This session was all about TDD, we were supposed to write the tests first. Writing a Cell class would be considered cheating in this excercise. Why? Simply because everything should be extracted from the test instead of written before. We want our tests to be a contract for our code, we do not know yet how this code will look like because TDD is supposed to give it the "correct" form.

How to apply this to our "Game"? Let's start with simple things like: *"if there are no alive cells then no cells can be born"*. Do we need anything to write this test? No! We do not need to create a `Cell` class we can even use a `Boolean` to express it. What about coordinates? What coordinates!? Our test doesn't say anything about any position at all so we don't need it. It's important to focus on defining the contract.

When to extract? Whenever you will see some duplication. By duplication I mean when the same idea is repeated not the same code (there is a difference). Now this is the tricky part how to spot such duplication? A simple example:

* *"if there are no alive cells then no cells can be born"*
* *"if there is only one alive cell then no cells will born or survive"*

There is a vague duplication of an idea. Idea of changing a set of cells to another set of cells. Now we could probably extract some function that takes the state of the "Game" and returns another. The same thing goes for other more detailed tests, at some point our `Boolean` cell won't be enough and we will refactor it to something better suiting our needs. Important thing is to create only things that are needed by the test to pass and nothing more!

### Retrospective 5

* best session of the day!
* it's easy to do extraction to early
* it's easy to do extraction to late

### Final thoughts

Unfortunately I could not stay for the final sixth session - working with legacy code. Idea was simple, people should not delete their solutions after session 5 and just swap laptops between pairs. Every session was great but that TDD one made the day for me, definitely time well spent.
