---
layout: post
date: 20-03-2023
author: Andrzej Jóźwiak
title: Should a player have a board?
tags: kotlin programming design architecture game-design
disqus: true
---

How to model a domain for a board game? What should the game object contain? This is especially interesting question if one would like to have a fully immutable code. The model of a game where players are represented by pawns on a common board should not bring much problems. On the other hand what should happen if players have their own personal boards? Simple? Then what if we want to have a different board size depending on the game state? Put immutability into the mix and updating the game state becomes a hussle. Creating a board game on paper seems simpler in this regard. Let's have a look!

In the game I am working on currently the player has a personal board where cards are placed, a hand of cards. The game can have multiple players, a common deck of cards, a common market of cards. This should be sufficient introduction to the domain. Let's start with defining the basics.

We don't want to focus yet on what a single `Card` represents and what is it built out of, thus we can just model it as a `String`. It is sufficient enough. Other basic building blocks of the game can be expressed in terms of `Lists`. A `Deck` is just a list of cards, the same goes for `Market` or cards held by the `Player` in `Hand`. All of them have certain properties and behaviours like limited size or only being able to get (draw) the element from top, at this stage of design it is not yet worth to think about them though.

```kotlin
typealias Card = String
typealias Deck = List<Card>
typealias Market = List<Card>
typealias Hand = List<Card>
typealias Board = List<List<Card>>
```

The `Card`, `Deck`, `Market` and `Hand` was easy. Now it is time for the players and the current game state. The obvious solution would be to allow the `Player` object to contain the `Board` as it is a dedicated element the same way the `Player` has a dedicated `Hand` of cards. Game state would contain a list of `Players`, current `Deck` and current `Market`.

```kotlin
data class Player(
    val id: UUID,
    val board: Board,
    val hand: Hand,
)

data class Game(
    val players: List<Player>,
    val deck: Deck,
    val market: Market,
)
```

On the surface level it looks like we finished it but let's think about usage of such a model. It is immutable. The best way would be to pick some game scenario and check how it would affect the game state.

The scenario for testing:
1. `Player` **selects** a `Card` from the common `Market` and **puts** in the `Hand`
2. `Player` **draws** a `Card` from the `Deck` and **places** it on the common `Market`
3. `Player` **selects** a `Card` from the `Hand` and **places** it on the `Board`

Not a truely surprising order of actions for anyone playing board games.

```kotlin
var currentState = Game(...) // some current state of the game
var currentPlayerId = UUID(...) // current player by UUID

// Player selects a Card from the common Market and puts in the Hand
val selectedMarketCard = currentState.market.get(1) // player selected card on the index 1

currentState = currentState.copy(
    market = currentState.market.removeAt(1),
    players = currentState.players.map {
        // we need to modify only the current player on the player list
        if (it.id == currentPlayerId) {
            // we want to keep other values of the player intact
            it.copy(
                hand = it.hand + selectedMarketCard
            )
        } else {
            // if this is not the current player then just return the info
            it
        }
    },
)

// Player draws a Card from the Deck and places it on the common Market
val newMarketCard = currentState.deck.get(0) // card from the top

currentState = currentState.copy(
    deck = currentState.deck.drop(1),
    market = currentState.market + newMarketCard,
)

// Player selects a Card from the Hand and place it on the Board
val selectedHandCard = currentState.players.first { it.id == currentPlayerId }.hand.get(2) // player selected card on index 2

currentState = currentState.copy(
    players = currentState.players.map {
        // we need to modify only the current player on the player list
        if (it.id == currentPlayerId) {
            it.copy(
                hand = it.hand - selectedHandCard,
                board = // ... now we need to modify an immutable List of immutable Lists uff
            )
        } else {
            // if this is not the current player then just return the info
            it
        }
    }
)

```

That was a lot of code to handle a few actions for such a small and concise model. No one will argue that it is not the most beautifull code in the world. Ofcourse it could be more concise.

```kotlin
var currentState = Game(...) // some current state of the game
var currentPlayerId = UUID(...) // current player by UUID


val selectedMarketCard = currentState.market.get(1) // player selected card on the index 1
val newMarketCard = currentState.deck.get(0) // card from the top
val selectedHandCard = currentState.players.first { it.id == currentPlayerId }.hand.get(2) // player selected card on index 2

currentState = currentState.copy(
    deck = currentState.deck.drop(1),
    market = currentState.market.removeAt(1) + newMarketCard,
    players = currentState.players.map {
        // we need to modify only the current player on the player list
        if (it.id == currentPlayerId) {
            // we want to keep other values of the player intact
            it.copy(
                hand = it.hand + selectedMarketCard - selectedHandCard,
                board = // ... now we need to modify an immutable List of immutable Lists uff
            )
        } else {
            // if this is not the current player then just return the info
            it
        }
    },
)
```

But I dislike it even more when it is like this. It's much more complicated to understand what is really happening but if the code length is what we are looking for...

It seems that the immutability caused us the most trouble but what about using the model for other concerns? Current `Player` is simple, the type only has `id`, `hand` and `board` what about other characteristics? What if we want our players to select a name? or pick a color? Should it be part of the same type?

```kotlin
data class Player(
    val id: UUID,
    val name: String,
    val color: Color,
    val board: Board,
    val hand: Hand,
)
```

Doesn't seem that bad, but let's look at the problem of creating a new game.

```kotlin
fun newGame(
    players: List<Player>,
    deck: Deck,
): Game = // ...
```

Passing a `List` of `Cards` as a `Deck` is simple but how can I pass correctly instantiated `Players`? The `Player` has a `Board` that needs to be passed to its constructor but how can we know the size of the `Board` before the game starts?

```kotlin
data class Player(
    val id: UUID,
    val name: String,
    val color: Color,
    val board: Board = emptyList(),
    val hand: Hand = emptyList(),
)
```

This solves the problem but only because the `Board` is implemented as a `List<List<Card>>` if we would like to have something with a specified `width` and `height` and we would like the game to define it, so how can we do it? By using `null`?

```kotlin
data class Player(
    val id: UUID,
    val name: String,
    val color: Color,
    val board: Board?,
    val hand: Hand?,
)
```

It does not look good and it allows for starting a new game with any illegal combination of arguments like `null` board and a hand containing a single card. Do we want it? Let's assume that `nulls` do not exist completely. Let's for a moment think about the physical world. A person becomes a `Player` and gets a `Board` and a `Hand` of `Cards` when participating in the game.

```kotlin
data class Player(
    val id: UUID,
    val name: String,
    val color: Color,
)

data class Game(
    val players: List<Player>,
    val hands: Map<Player, Hand>,
    val boards: Map<Player, Board>,
    val deck: Deck,
    val market: Market,
)
```

This approach definitely solves the problem of using the `Player` type outside of the `Game` context, but the `Game` alone became even more complex. If a `Player` before a game and `Player` during a game is a different type of `Player` then why not try to model it.

```kotlin
data class Participant(
    val id: UUID,
    val name: String,
    val color: Color,
)

data class Player(
    val id: UUID,
    val name: String,
    val color: Color,
    val board: Board,
    val hand: Hand,
)

data class Game(
    val players: List<Player>,
    val deck: Deck,
    val market: Market,
)

fun newGame(
    participants: List<Participant>,
    deck: Deck,
): Game = // ...
```

We created a new domain object called `Participant`, although it made the `Game` simpler again it created a not so obvious issue. It created a new concept, that is a synonymous with `Player` in the collective consciousness. This is problematic as the code is read more times then it is written. This is why when introducing such concepts, entities one needs to think how others will understand it. So maybe the problem is in the too concise naming?

We can have a "new player" and an "active player", so maybe we should model it as such.

```kotlin
data class NewPlayer(
    val id: UUID,
    val name: String,
    val color: Color,
)

data class ActivePlayer(
    val id: UUID,
    val name: String,
    val color: Color,
    val board: Board,
    val hand: Hand,
)
```

The word "active" is also not good because it implies that there is also a concept of "inactive" players. Maybe it is not as bad but still make one wonder. We yet have to solve the issue of accessing the `List` of `Players` and updating it.

```kotlin
data class Game(
    val players: List<ActivePlayer>,
    val deck: Deck,
    val market: Market,
)
```

If you look at it, the `List` is the problem, can we have a game with no players, one player or twenty players? The number of players in the game is limited. In the case of the game I am working on there can be only two, three or four players. Maybe this can be modeled and it will allow to flatten the game state.

```kotlin
sealed interface Game {
    val deck: Deck
    val market: Market

    data class TwoPlayerGame(
        val first: ActivePlayer,
        val second: ActivePlayer,
        override val deck: Deck,
        override val market: Market,
    ) : Game

    data class ThreePlayerGame(
        val first: ActivePlayer,
        val second: ActivePlayer,
        val third: ActivePlayer,
        override val deck: Deck,
        override val market: Market,
    ) : Game

    data class FourPlayerGame(
        val first: ActivePlayer,
        val second: ActivePlayer,
        val third: ActivePlayer,
        val forth: ActivePlayer,
        override val deck: Deck,
        override val market: Market,
    ) : Game
}
```

The flattened structure allows for simpler access.

```kotlin
// we are working with the first player of a two player game
var currentState = TwoPlayerGame(...) // some current state of the game

// Player selects a Card from the common Market and puts in the Hand
val selectedMarketCard = currentState.market.get(1) // player selected card on the index 1

currentState = currentState.copy(
    market = currentState.market.removeAt(1),
    firstPlayer = currentState.firstPlayer.copy(
        hand = currentState.firstPlayer.hand + selectedMarketCard
    ),
)

// Player draws a Card from the Deck and places it on the common Market
val newMarketCard = currentState.deck.get(0) // card from the top

currentState = currentState.copy(
    deck = currentState.deck.drop(1),
    market = currentState.market + newMarketCard,
)

// Player selects a Card from the Hand and place it on the Board
val selectedHandCard = currentState.firstPlayer.hand.get(2) // player selected card on index 2

currentState = currentState.copy(
    firstPlayer = currentState.firstPlayer.copy(
        hand = currentState.firstPlayer.hand - selectedHandCard,
        board = // ... now we need to modify an immutable List of immutable Lists uff
    )
)
```

Much better then the previous attempt but still quite a bit of calls to `copy`. How about flattening it more.

```kotlin
data class Player(
    val id: UUID,
    val name: String,
    val color: Color,
)

data class TwoPlayerGame(
    val firstPlayer: Player,
    val firstPlayerHand: Hand,
    val firstPlayerBoard: Board,

    val secondPlayer: Player,
    val secondPlayerHand: Hand,
    val secondPlayerBoard: Board,

    val deck: Deck,
    val market: Market,
)
```

Now the same steps of processing are easier to comprehend.

```kotlin
// we are working with the first player of a two player game
var currentState = TwoPlayerGame(...) // some current state of the game

// Player selects a Card from the common Market and puts in the Hand
val selectedMarketCard = currentState.market.get(1) // player selected card on the index 1

currentState = currentState.copy(
    market = currentState.market.removeAt(1),
    firstPlayerHand = currentState.firstPlayerHand + selectedMarketCard,
)

// Player draws a Card from the Deck and places it on the common Market
val newMarketCard = currentState.deck.get(0) // card from the top

currentState = currentState.copy(
    deck = currentState.deck.drop(1),
    market = currentState.market + newMarketCard,
)

// Player selects a Card from the Hand and place it on the Board
val selectedHandCard = currentState.firstPlayerHand.get(2) // player selected card on index 2

currentState = currentState.copy(
    firstPlayerHand = currentState.firstPlayerHand - selectedHandCard,
    firstPlayerBoard = // ... now we need to modify an immutable List of immutable Lists uff
)
```


Better? Depends on the taste but at least there is no need to iterate over a list. Working with immutable data structures is hard in Kotlin. In Haskell or Scala there are libraries that introduce the concepts of lenses and optics, making changes to nested immutable type structures easier. In Kotlin we have an option to use a library like `Arrow-Optics` or flatten what we can during design and wonder should the player have a board.
