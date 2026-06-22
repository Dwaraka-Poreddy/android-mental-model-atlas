# State Holder

## A New Observation

In the previous chapter we learned:

```text
State = The Current Situation Of An Application
```

For example:

```text
Username: dwaraka

Loading: false

Logged In: true
```

represents the current state of a login screen.

A natural question appears:

```text
Where Is This Current Situation Actually Kept?
```

If the application knows:

```text
Current Song

Current User

Current Shopping Cart

Current Login Status
```

then something must be storing that information.

This chapter explores that idea.

---

## A Whiteboard

Imagine entering a meeting room.

On the whiteboard you see:

```text
Sprint Planning 10:00 AM Meeting Room A
```

Later someone erases it and writes:

```text
Sprint Review 2:00 PM Meeting Room C
```

The information changed.

But one thing stayed the same.

```text
The Whiteboard Continues To Hold The Current Information
```

---

## Another Example

Imagine a classroom.

An attendance board shows:

```text
Present: 28

Absent: 2
```

A student arrives.

Now it becomes:

```text
Present: 29

Absent: 1
```

The numbers change.

The board does not.

It always holds:

```text
The Current Attendance
```

---

## State Holder

Applications work in the same way.

State tells us:

```text
What The Current Situation Is
```

A state holder tells us:

```text
Where That Current Situation Is Kept
```

Conceptually:

```text
Current Situation

↓

State

↓

State Holder
```

---

## Another Mental Model

Imagine a photograph.

The photograph captures:

```text
One Snapshot Of Reality
```

But where is that photograph stored?

```text
Photo Album
```

Conceptually:

```text
Snapshot

↓

Photo Album
```

Applications behave similarly.

```text
State

↓

State Holder
```

The state holder keeps the latest snapshot.

---

## Android Example

Suppose we build a music player.

Its current state is:

```kotlin
data class PlayerState(

    val songName: String,

    val isPlaying: Boolean,

    val progressSeconds: Int,

    val volume: Int
)
```

At runtime, something must keep an instance of this object.

```kotlin
class MusicPlayer {

    private var playerState = PlayerState(

        songName = "",

        isPlaying = false,

        progressSeconds = 0,

        volume = 50
    )
}
```

Here:

```text
PlayerState

↓

Current Situation
```

while:

```text
MusicPlayer

↓

State Holder
```

---

## Another Android Example

Consider a shopping cart.

```kotlin
data class CartState(

    val itemCount: Int,

    val totalPrice: Double,

    val isCheckoutEnabled: Boolean
)
```

Something must always know:

```text
How Many Items Are In The Cart Right Now
```

A possible implementation:

```kotlin
class ShoppingCart {

    private var cartState = CartState(

        itemCount = 0,

        totalPrice = 0.0,

        isCheckoutEnabled = false
    )
}
```

Again:

```text
ShoppingCart

↓

State Holder

↓

CartState
```

---

## Another Android Example

A login screen might use:

```kotlin
data class LoginState(

    val username: String,

    val password: String,

    val isLoading: Boolean,

    val isLoggedIn: Boolean
)
```

Some object keeps the latest login state.

```kotlin
class LoginManager {

    private var loginState = LoginState(

        username = "",

        password = "",

        isLoading = false,

        isLoggedIn = false
    )
}
```

The object holding the current login state is acting as:

```text
A State Holder
```

---

## Another Common Misconception

A common misconception is:

```text
State Automatically Exists
```

Incorrect.

Some object must always:

```text
Own It

Store It

Update It
```

Otherwise there would be no current situation to read.

---

## Another Common Misconception

A common misconception is:

```text
Every Object Is A State Holder
```

Incorrect.

An object becomes a state holder only if its responsibility includes keeping:

```text
The Current Situation
```

Many objects simply perform work without storing any long-lived state.

---

## State vs State Holder

State answers:

```text
What Is The Current Situation?
```

State holder answers:

```text
Where Is The Current Situation Kept?
```

Conceptually:

```text
State

↓

Current Situation

-------------------

State Holder

↓

Keeps Current Situation
```

---

## The Key Mental Model

Imagine a whiteboard.

The writing may change:

```text
Sprint Planning

↓

Sprint Review

↓

Retrospective
```

But the whiteboard continues holding:

```text
The Latest Information
```

Applications behave the same way.

```text
Current Situation

↓

State

↓

State Holder
```

The state holder always keeps the latest snapshot of the application's current situation.

---

## Revision Table

| Section | Content |
|----------|----------|
| Core Idea | A state holder keeps the current state |
| Mental Model | Whiteboard / Photo Album |
| State Answers | What is the current situation? |
| State Holder Answers | Where is the current situation kept? |
| Android Examples | Music Player, Shopping Cart, Login Screen |
| Common Misconception | State does not exist on its own |
| Previous Concept | State |
| Next Concept | Characteristics Of State |
