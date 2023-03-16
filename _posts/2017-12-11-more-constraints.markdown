---
layout: post
date: 11-12-2017
author: Andrzej Jóźwiak
title: Even more constraints for the game of life
tags: kotlin functional-programming fp coderetreat
disqus: true
---

On the last [Coderetreat](http://ajoz.github.io/2017/11/19/coderetreat-lodz-2017/) organized by [JUG Lodz](https://www.meetup.com/Java-User-Group-Lodz/) I wanted to try something different. As coderetreat is most often about Object Oriented programming and Test Driven Development thus sessions revolve around [Object Calisthenics](http://williamdurand.fr/2013/06/03/object-calisthenics/). Still I wanted to try out something different, and implement the [Game of Life](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life) with a Functional Programming approach.

FP comapring to OO ment using a completely different set of constraints. I decided upon these:
- no unnamed lambdas (no matter how tempting they are)
- no mutable state
- no explicit loops (for, while)
- state decoupled from the behaviour (no OO)
- side effects at the boundries of the "system" (pure functions should not call impure code)
- expressions used instead of statements
- Kotlin all the way (No JAVA!)

I also thought about some bonus (nice to have) constraints:
- one argument functions (this ment currying and partial application)
- point-free notation wherever possible

There is a nice article summing up and explaining all of the Functional Programming [Calisthenics](https://codurance.com/2017/10/12/functional-calisthenics/#oneargumentfunctions), anyone wanting to try this should read it first.

I have also decided to make some constraints about the game representation:
- map can be infinite in size
- only alive cells exist in the world (no separation into a Live and Dead cell)

### Where to begin?

Game of life is a simple design problem but can be approached in two ways.

One can do it "bottom to top", so start with defining a `Cell` class and then slowly going up by adding a `Board` containing all the cells and a `Game` encapsulating the `Board` and performing the simulation.

```kotlin
class Cell
class Board
class Game
```

This approach is very tempting, because it allows one to focus on a very small "detail" of the designed system.

Another way would be the "top to bottom" design, so first starting with a `Game` class and designing its fascade and ways of communicating the results of the simulation rather then focusing on the `Board` or a `Cell`.

Although not straightforward it allows to come up with useful and comfortable way of working with the designed API.

### Boundry of the system

Kotlin does not have a distinction between pure and impure functions. So it was up to me to show enough discipline to avoid mixing IO stuff with pure domain logic.

Moving the side effects to the boundry of the designed system ment a few things.
- running the game (input/output) should be done by an impure function
- game alone does not have to know anything about a concept of iteration, it can be purely stateless

On the high level this all means that for start we need a pure function that takes current game state, moves it by a single iteration and returns a new game state.

```kotlin
fun nextGameState(gameState: State): State {
  // something here
}
```

This immediately forces us to define a `State`, but we do not yet know what it is and how should it behave. Here comes a really nice Kotlin feature, a `typealias`. 

This is a very, very handy feature. It allows us to define different names for existing types. What's so handy about it?

It greatly enhances readability and helps with lowering the cognitive context switching. I think it also allows to focus on the subject matter rather then the implementation details.

Types are handy but often can poison our thinking about a subject. Poison? **Yes**, poison in a sense that we will start to define things in terms of types we use. It doesn't sound so bad, but I prefere to read code like:

```kotlin
fun nextGameState(gameState: State): State {}
```

instead of the code:

```kotlin
fun nextGameState(gameState: List<Pair<Int, Int>>): List<Pair<Int, Int>> {}
```

At least for me, it's immediately distracting and leaks the details of the internal implementation. Our mind would immediately associate `State` with `List<Pair<Int, Int>>` which in turn can skew the future, often forbidding us from spreading our wings and moving the design to a different direction.

### How does the game work?

What does it mean to generate a next game state? The Game of Life has simple rules but let's review them first.

Wikipedia has the whole thing explained quite [well](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life), but we can distill the rules to a few things:

> Every cell interacts with its eight neighbours, which are the cells that are horizontally, vertically, or diagonally adjacent.

So we know that we will be operating on a two dimensional plane.

> Any live cell with two or three live neighbours survives.

So we need to handle exisiting cells of the generation.

> Any dead cell with three live neighbours becomes a live cell.

So an empty space that does not contain a cell can spawn a new cell.

> All other live cells die in the next generation. Similarly, all other dead cells stay dead.

Live cells with more then three neighbours die of the environment being too crowded, live cells with less then two neighbours die of loneliness. Our game should not support necromancy thus no spontanous cell's poping up should occur.

The rules above tell us that the cells can only appear or disappear near the live cells. So we need to find "candidate" positions that can have a new live cell, near each already living cell.

After we get the neighbourhood for each cell, we can count how many times certain position occurs. This number will tell us how many live cells are next to this "candidate" position.

We could think that a "candidate" is just a position plus a count of noighbouring cells.

Every candidate with less then **two** neighbours or more then **three** neighbours can be removed.

So what about already alive cells dying?

We get it for free! Yes! An alive cell will be a "candidate" of a cell next to it. If it occurs more then three times then it means that it is overcrowded and it dies, if it occurs less then two time then it means it is loenly and it dies. Like I said we have it for free.

### Getting the neighbourhood

First we need a `Position`

```kotlin
private data class Position(
    val x: Int,
    val y: Int
)
```

Second we need to get all neighbouring positions around a "cell". What is a cell then? For now we can just associate it with a `Position`.

```kotlin
typealias Cell = Position
```

Getting the needed positions is simple then:

```kotlin
fun Cell.getNeighbouringPositions(): List<Position> =
    listOf(
        Position(x - 1, y + 1), Position(x, y + 1), Position(x + 1, y + 1),
        Position(x - 1, y),      /*Position(x, y),    */ Position(x + 1, y),
        Position(x - 1, y - 1), Position(x, y - 1), Position(x + 1, y - 1)
    )
```

Our `Cell` is in the middle, thus we do not want it in the `List`.

### Creating candidates

We want to create "candidates", let's define them:

```kotlin
private data class Candidate(
    val position: Position,
    val neighbouringCellsCount: Int
)
```

Now having the candidate, we want to get all the candidates for every cell in the currently processed generation. What is a generation? It is the current iteration of cells, similarly to `Cell` we do not know yet how it should look like so we can use a typealias.

```kotlin
typealias Generation = Collection<Cell>
```

Now getting the candidates should be simple enough.

```kotlin
fun Generation.getCandidates(): List<Candidate> =
    flatMap { it.getNeighbouringPositions() }
        .groupBy(Position::hashCode)
        .map {
            Candidate(
                position = it.value[0],
                neighbouringCellsCount = it.value.size
            )
        }
```

1. We take the collection of `Cells` corresponding to the `Generation`
2. For each `Cell` we return its list of neighbouring `Positions`
3. As `getNeighbouringPositions` returns a `List<Position>` by simply using `map` over the generation we would get a `List<List<Position>>`, it will be much easier to work with a flattened structure, thus we are using a `flatMap`.
4. We group the `Positions`, we can relay on `hasCode` as `Position` is a data class and for the simple implementation it is sufficient. This looks weird because after grouping we will get a `Map` with a hashcode as a key and then a `List` containing the same position one or more times.
5. We `map` the groupping to a `Candidate`. As the key for the groupping is the hashcode we can use the first element of the `List` stored as a value to put as a position of the `Candidate`, the count of neighbouring cells is just the size of the `List`.

### Finding alive cells in the candidate list

```kotlin
fun Generation.isCandidateCellAlive(
    candidate: Candidate
): Boolean =
    candidate.neighbouringCellsCount == 3 || candidate.neighbouringCellsCount == 2 && contains(candidate.position)

```

An empty `Position` becomes a `Cell` if it has three neighbours. A `Cell` does not dies if has two neighbours.

To find next `Generation` of `Cells` we need to check each `Candidate` against current `Generation`

```kotlin
fun Generation.findLiveCells(
    candidates: List<Candidate>
): Generation =
    candidates
        .filter { isCandidateCellAlive(it) }
        .map { it.position }
```

This process is just a simple filtering, we will be left with only `Candidates` that correspond with new live `Cells`, as `Generation` is just a `Collection<Cell>` we can simply `map` the `Candidate` to the position it holds.

The complete code for generating a next step of the game:

```kotlin
fun nextGameState(gameState: Generation): Generation =
    findLiveCells(
        candidates = gameState.getCandidates()
    )
```

or simplified:

```kotlin
fun Generation.next(): Generation =
    findLiveCells(
        candidates = getCandidates()
    )
```

### Running the game

It would be nice to see if our hard work payed off. As mentioned at the begining we wanted to move the side effects to the edge of the designed system, thus there are no things related to drawing the game state yet.

We can store our initial game state in a form of a `String`:

```
_@_
@@@
_@_
```

Let's associate `@` with a live `Cell`. As in our case we do not have two dimnnsional array depicting the game board but a list of live `Cells` we need to draw "empty" spaces between `Cells`. For this we will use `_`.

Let's first change a `String` to a `Generation` of `Cells`

```kotlin
private fun String.asGeneration(
    delimiter: String = "\n",
    cellChar: Char = '@'
): Generation =
    if (isBlank()) emptyList()
    else {
        split(delimiters = delimiter)
            .mapIndexed { y, s ->
                s.mapIndexed { x, c -> Pair(c, Position(x, y)) }
            }
            .flatten()
            .filter { pair -> pair.first == cellChar }
            .map { pair -> pair.second }
    }
```

First we want to split the string into lines. For the string:

```
_@_
@@@
_@_
```

and a new line as the delimiter we will get a list:

```
_@_, @@@, _@_
```

Position in the list corresponds to the `y` axis. Position of the `char` in the `String` corresponds to the `x` axis.

Through this we can calculate a `Pair` of `Char` and a `Position`. This `Char` will be used to detect if this `Position` corresponds to a live `Cell` or not.

We need to do the same thing but in the opposite direction.

```kotlin
fun Generation.asString(
    cellMarker: String = "@",
    emptyMarker: String = " ",
    delimiter: String = "\n"
): String =
    if (isEmpty())
        "[empty]\n"
    else {
        val minX = minBy { cell -> cell.x }?.x ?: 0
        val maxX = maxBy { cell -> cell.x }?.x ?: 0
        val minY = minBy { cell -> cell.y }?.y ?: 0
        val maxY = maxBy { cell -> cell.y }?.y ?: 0

        (minX..maxX)
            .flatMap { x ->
                (minY..maxY)
                    .map { y ->
                        if (contains(Position(x, y)))
                            cellMarker
                        else
                            emptyMarker
                    }
                    .plus(delimiter)
            }
            .joinToString(separator = "")
    }
```

The hardest part of the thing is doing the iteration over the `Cells` as their position list that corresponds to the `Generation` is not guaranteed to be ordered.

The above solution is very suboptimal. It finds the range of coordinates and checks if there is `Cell` corresponding to it. If there is then it draws `@`, otherwise it draws `_`.

We need something that will:
- read the initial generation
- perform the game in a loop
- on each iteration print the current state of the game
- wait a few milliseconds for us to see

```kotlin
tailrec fun runGame(
    generation: Generation,
    numberOfIterations: Int,
    sleepTime: Long = 500
): Unit =
    when (numberOfIterations) {
        0 -> println("Game finished!")
        else -> {
            println("Iteration: $numberOfIterations")
            println(generation.asString())
            Thread.sleep(sleepTime)
            runGame(
                generation = generation.next(),
                numberOfIterations = numberOfIterations - 1
            )
        }
    }
```

As this a recursive function, we mark it as `tailrec` to get the glorious benefits of tailed recurssion.

```kotlin
fun main(args: Array<String>) {
    val initial =
        """
            _@_
            @@@
            _@_
            """.trimIndent()

    runGame(
        generation = initial.asGeneration(),
        numberOfIterations = 100
    )
}
```

The result of our hard work:

```kotlin
Iteration: 100
 @ 
@@@
 @ 

Iteration: 99
@@@
@ @
@@@

Iteration: 98
  @  
 @ @ 
@   @
 @ @ 
  @  

Iteration: 97
...
```

### Finishing thoughts

Let's do a retrospection what was written and if all the constraints were met.

> no unnamed lambdas (no matter how tempting they are)

We have a few. All calls to `map`, `flatMap`, `filter`, but we did not create our own. So I call it a win.

> no mutable state

No problems here, the whole solution is immutable.

> no explicit loops (for, while)

No problems here, we did not use `forEach` or `onEach` either.

> state decoupled from the behaviour (no OO)

We did not create any explicit functions for the types we've introduced. The extension functions are just static functions with some syntactic sugar, so I count this as a win.

> side effects at the boundries of the "system" (pure functions should not call impure code)

The impure `print` or `println` is called only at the boundry i.e. `runGame`. Another win.

> expressions used instead of statements

I think we did this.

> Kotlin all the way (No JAVA!)

There is no doubt about that.

> one argument functions (this ment currying and partial application)
> point-free notation wherever possible

Kotlin is not a great language to do it. I've already wrote an article once about possible issues with currying. The Kotlin's syntax was not built to accommodate for this. After trying a bit I thought it will be much better to do just some idiomatic Kotlin rather then writing it in a way that will be foreign to most of the readers.

> map can be infinite in size

It can!

> only alive cells exist in the world (no separation into a Live and Dead cell)

`Cell` is just a `Position`!

Constraints are great!