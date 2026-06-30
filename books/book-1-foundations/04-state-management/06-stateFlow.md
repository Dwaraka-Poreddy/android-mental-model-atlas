# StateFlow

## Looking Back

In the previous chapter we discovered that representing state requires:

```text
Current Value + Future Updates
```

This naturally led to the need for a new abstraction.

That abstraction is:

```text
StateFlow
```

---

## StateFlow

A StateFlow represents:

```text
The Current State Of Something
```

while continuously providing updates whenever that state changes.

Conceptually:

```text
Current State + Future State Updates

↓

StateFlow
```

---

## Another Mental Model

Imagine a digital notice board.

It currently displays:

```text
Lunch Break

1:00 PM
```

Someone walks by.

They immediately know:

```text
Lunch Break

1:00 PM
```

A few minutes later, the notice changes to:

```text
Team Meeting

2:00 PM
```

Everyone looking at the board automatically sees:

```text
Team Meeting

2:00 PM
```

The notice board always provides:

```text
The Current Notice + Future Notice Changes
```

A StateFlow behaves in the same way.

---

## Android Example

Suppose we are building a music player.

Its current state is represented as:

```kotlin
data class PlayerState(

    val songName: String,

    val isPlaying: Boolean,

    val progressSeconds: Int
)
```

The application exposes the player's state as:

```kotlin
val playerState: StateFlow<PlayerState>
```

Any part of the application interested in the player can observe this StateFlow.

---

## Another Android Example

Consider a shopping cart.

```kotlin
data class CartState(

    val itemCount: Int,

    val totalPrice: Double
)
```

The current cart state is exposed as:

```kotlin
val cartState: StateFlow<CartState>
```

Every consumer immediately knows:

```text
Current Item Count

Current Total Price
```

and continues receiving updates whenever the cart changes.

---

## Observing A StateFlow

StateFlow is collected just like the Flow we learned previously.

```kotlin
viewModel.cartState.collect { state ->

    println(state.itemCount)

}
```

The important difference is conceptual.

The collector receives:

```text
The Current State

↓

Future State Updates
```

instead of waiting only for future changes.

---

## What Does A StateFlow Represent?

A StateFlow always represents:

```text
Exactly One Current State
```

For example:

```text
Loading: false
```

may later become:

```text
Loading: true
```

and finally:

```text
Loading: false
```

At every moment there is exactly one current value.

---

## Another Mental Model

Think of a scoreboard.

It currently shows:

```text
130 / 3
```

A spectator arrives.

They immediately see:

```text
130 / 3
```

When the next ball is hit, it updates to:

```text
134 / 3
```

Everyone now sees:

```text
134 / 3
```

The scoreboard always represents:

```text
The Current Match Situation
```

rather than the complete history of the match.

A StateFlow behaves in exactly the same way.

---

## Android Example

A login screen may expose:

```kotlin
data class LoginState(

    val username: String,

    val isLoading: Boolean,

    val isLoggedIn: Boolean
)
```

using:

```kotlin
val loginState: StateFlow<LoginState>
```

Different parts of the UI can observe the same StateFlow and always know the current login state.

---

## Another Common Misconception

A common misconception is:

```text
StateFlow Stores Every Previous State
```

Incorrect.

StateFlow represents:

```text
The Current State
```

As the state changes, the current value changes.

Its primary responsibility is not preserving history but representing the latest situation.

---

## Another Common Misconception

A common misconception is:

```text
StateFlow Is Just A Flow
```

StateFlow is built on the same reactive ideas but has a different responsibility.

It is specifically designed to represent:

```text
State
```

which naturally requires:

```text
Current Value + Future Updates
```

---

## Putting Everything Together

```text
Shopping Cart

↓

Current Cart State

↓

StateFlow

↓

Current Cart

+

Future Cart Updates
```

```text
Music Player

↓

Current Player State

↓

StateFlow

↓

Current Playback State

+

Future Playback Updates
```

---

## The Key Mental Model

Think of a StateFlow as a digital notice board.

At every moment it knows:

```text
The Current Information
```

Whenever that information changes:

```text
Everyone Looking Sees The Update
```

Conceptually:

```text
Current State + Future Updates

↓

StateFlow
```

---

> **You'll see this in...**
> - **DOC 5 — Android Lifecycle & Architecture**, where every ViewModel exposes screen state as `StateFlow<UiState>` — this becomes the standard pattern
> - **DOC 7 — Jetpack Compose**, where `collectAsState()` turns StateFlow into Compose state, triggering recomposition on every emission
> - **DOC 13 — Data Architecture**, where Repositories convert cold database Flows into hot StateFlows using `stateIn()`

---

## Revision Table

| Section | Content |
|----------|----------|
| Core Idea | StateFlow represents the current state and its future updates |
| Mental Models | Digital notice board, Scoreboard |
| Represents | One current state |
| Android Examples | Music player, Shopping cart, Login screen |
| StateFlow Provides | Current value + future updates |
| Common Misconception | StateFlow is not a history of all previous states |
| Previous Concept | Why StateFlow Exists |
| Next Concept | MutableStateFlow |
