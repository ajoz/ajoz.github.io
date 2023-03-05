---
layout: post
date: 05-03-2023
author: Andrzej Jóźwiak
title: Tell what I can do, not what I cannot!
tags: kotlin programming design architecture game-design
disqus: true
---

Recently I started to write a small card game in my spare time. Nothing big and fancy like Magic The Gathering or Hearthstone. Just a small card game where players create their own plaid out of cards depicting colored patterns. Of course I started with designing the model that will contain the concepts of the game, so things like card, patterns, board and a deck. Quite interestingly this deck made me think. Is it just a simple list of cards? Or even more precisely, is it just a simple stack of cards?

From an engineering perspective a deck of cards is just a sorted or randomized list of cards. Players are able to draw the cards, shuffle the deck, do other actions depending on the game.

So should we just use a simple list of cards to model our game if we want a deck?

```kotlin
typealias Deck = List<Card>
```

When begining with a design, a typealias should work just fine but the problem comes when we want to add more specific domain specific functions like `draw` or `shuffle` it starts to be more complicated.

A simple `draw` can be implemented in terms of `drop(1)` and `first()` or `take(1)`, although both `drop(1)` and `take(1)` functions in Kotlin can be safely run on an empty list, the `first()` function cannot. Besides this we also need to come up with a proper signature for the `draw`.

Most probably a self contained `draw` function that returns the drawn cards and the remaining deck would be the best thing. To make things simpler let's just make it return a `Pair` of list of drawn cards and a list of remaining cards in the deck.

```kotlin
fun List<Card>.draw(atLeast: Int): Pair<List<Card>, List<Card>> {}
```

The signature looks fine we can use a bit of type aliases to make it more readable.

```kotlin
typealias Deck = List<Card>
typealias Draw = List<Card>

fun Deck.draw(atLeast: Int): Pair<Draw, Deck> {}
```

The implementation is fairly simple (as we are not looking into doing any optimization). We can just take elements from the list and drop the same amount to get the remaining deck.

```kotlin
fun Deck.draw(atLeast: Int): Pair<Draw, Deck> {
    val drawn = take(atLeast)
    val remaining = drop(atLeast)
    return drawn to remaining
}
```

One might argue that the code above looks strange mainly because it strives to be immutable. If a `MutableList<Card>` would be used instead the code would be much simpler.

```kotlin
typealias Deck = MutableList<Card>
typealias Drawn = List<Card>

fun Deck.draw(atLeast: Int): Drawn {}
```

The signature looks much cleaner, simpler the internals would most probably use a combination of `remoteAt(index)` and iteration.

Adding a specialized version of the `draw()` method that only draws a single card seems simple.

```kotlin
fun Deck.draw(): Card {
    return draw(atLeast = 1).first()
}
```

What will happen if the `Deck` is empty? There are several options available. Throwing a dedicated runtime exception.

```kotlin
@Throws(DeckIsEmpty::class)
fun Deck.draw(): Card {}
```

Using a nullable return type to indicate possible problem.

```kotlin
fun Deck.draw(): Card? {
    return draw(atLeast = 1).firstOrNull()
}
```

Working with such methods is more like doing API or DB queries and waiting for a response.

Would encapsulating the `MutableList<Card>` in a dedicated type help?

```kotlin
class Deck(
    private val cards: List<Card>,
) {
    fun draw(): Card? { }
    fun isEmpty(): Boolean { }
    fun isNotEmpty(): Boolean { }
}
```

Other then avoiding exposing additional (not domain specific) functions, there problems are the same.

```kotlin
val deck = Deck(...)
// ...
val card = deck.draw()
card?.type?.otherMethod() ?: defaultValueIfNull

// or
if (deck.isNotEmpty()) {
    val card = deck.draw()
} else {
    // when the deck is empty do something else
}
```

The need for query stays, either by checking if the deck `isEmpty` or `isNotEmpty` or using an elvis operator and adding `?:` to handle the nullability.

Can an empty deck only expose methods that can only really be invoked on it? Let's also try to make the `Deck` immutable.

```haskell
data Deck = Empty | Containing NonEmptyList Card
```

Oops, it's not Kotlin, but it clearly presents our intentions. A deck of cards can be empty or it can contain a non empty list of cards.

```kotlin
sealed interface Deck {
    object Empty : Deck

    data class Containing(
        private val cards: NonEmptyList<Card>,
    ) : Deck {
        fun draw(): Card { }
        fun draw(atLeast: Int): NonEmptyList<Card> { }
    }
}
```

To make the solution immutable we need the `Deck` to return a new modified instance after the method is called.

```kotlin
typealias Drawn = NonEmptyList<Card>

sealed interface Deck {
    object Empty : Deck

    data class Containing(
        private val cards: NonEmptyList<Card>,
    ) : Deck {
        // let's focus only on a single method for now.
        fun draw(atLeast: Int): Pair<Drawn, Deck> { }
    }
}
```

The `Drawn` typealias can be changed from the `List<Card>` to a `NonEmptyList<Card>` as we are sure that it contains at least a single card. Usage is simple if we have an instance.

```kotlin
val deck = Deck.Containing(...)
val (drawn, remainingDeck) = deck.draw(atLeast = 2)
```

What if we only get a `Deck` then we return to square one?

```kotlin
val deck: Deck = ...

when (deck) {
    is Deck.Empty -> { ... }
    is Deck.Containing -> {
        val (drawn, remainingDeck) = deck.draw(atLeast = 2)
        // other processing ...
    }

}
```

The whole exercise seems pointless. There is no real difference between checking `isEmpty` and checking `is Deck.Empty`. Technically there is not but we have an added benefit that the `isEmpty` version does not have. What it might be?

This sealed hierarchy can be used for other parts of the domain, wherever it is expected to pass a deck containing at least one card.

```kotlin
fun foo(deck: Deck.Containing) { }

//vs

fun foo(deck: Deck) { }
```

It is most importantly not possible to invoke certain method by mistake or without proper preparation.

The code should tell what is possible, not what cannot be done!