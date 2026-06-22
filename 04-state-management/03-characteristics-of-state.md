# Characteristics Of State

## A New Observation

In the previous chapters we learned:

```text
State

↓

Current Situation
```

and

```text
State Holder

↓

Keeps The Current Situation
```

A natural observation appears.

Suppose we open a music player.

Initially:

```text
Song: Believer

Playing: true

Progress: 01:42
```

One second later:

```text
Song: Believer

Playing: true

Progress: 01:43
```

Another second later:

```text
Song: Believer

Playing: false

Progress: 01:43
```

The state changed.

But something interesting happened.

We no longer care about:

```text
01:42
```

We care about:

```text
01:43
```

The application always focuses on:

```text
The Latest Situation
```

---

## Characteristic 1

### State Always Has One Current Value

At any moment there is exactly one:

```text
Current Song

Current Progress

Current Playback Status
```

Never:

```text
Two Current Songs

Or

Two Current Playback States
```

Conceptually:

```text
Current Situation

↓

One Current Value
```

---

## Another Example

Imagine a shopping cart.

Initially:

```text
Items: 2
```

The user adds another item.

Now:

```text
Items: 3
```

The current cart is:

```text
Items: 3
```

The application does not display:

```text
Items: 2

AND

Items: 3
```

at the same time.

There is always:

```text
One Current Cart State
```

---

## Characteristic 2

### State Changes Over Time

State is not constant.

It evolves.

For example:

```text
Loading: false

↓

Loading: true

↓

Loading: false
```

or:

```text
Temperature:
31°C

↓

32°C

↓

33°C
```

State is expected to change.

What makes it state is not stability.

It is:

```text
Representing The Current Situation
```

---

## Android Example

A login screen may use:

```kotlin
data class LoginState(

    val username: String,

    val isLoading: Boolean,

    val isLoggedIn: Boolean
)
```

Initially:

```kotlin
LoginState(

    username = "dwaraka",

    isLoading = false,

    isLoggedIn = false
)
```

When login starts:

```kotlin
LoginState(

    username = "dwaraka",

    isLoading = true,

    isLoggedIn = false
)
```

After success:

```kotlin
LoginState(

    username = "dwaraka",

    isLoading = false,

    isLoggedIn = true
)
```

Each object represents a different current situation.

---

## Characteristic 3

### The Latest Value Is Usually The Most Important

Imagine checking the weather.

You open the app and immediately want to know:

```text
Current Temperature
```

not:

```text
Temperature 10 Minutes Ago
```

Similarly, a music player cares about:

```text
Current Progress
```

not:

```text
Progress Five Seconds Ago
```

Applications usually need:

```text
The Latest State
```

to make decisions and update the UI.

---

## Another Mental Model

Imagine a cricket scoreboard.

Initially:

```text
120 / 2
```

A few minutes later:

```text
124 / 2
```

Then:

```text
130 / 3
```

Spectators do not need every previous scoreboard.

They simply look at:

```text
130 / 3
```

because it represents:

```text
The Current Match Situation
```

Applications treat state in the same way.

---

## Characteristic 4

### Anyone Reading State Expects The Current Value

Suppose a new spectator enters the stadium.

They immediately look at:

```text
130 / 3
```

They do not expect to first see:

```text
120 / 2

↓

124 / 2

↓

130 / 3
```

Similarly, when a screen needs state, it expects:

```text
The Current State Right Now
```

rather than the complete history of changes.

---

## Another Android Example

Consider a cart screen.

```kotlin
data class CartState(

    val itemCount: Int,

    val totalPrice: Double
)
```

The UI simply wants:

```text
Current Item Count

Current Total Price
```

It does not need every previous cart value to display the screen.

---

## Another Common Misconception

A common misconception is:

```text
State Never Changes
```

Incorrect.

State changes continuously.

Its responsibility is to represent:

```text
The Current Situation
```

at every moment.

---

## Another Common Misconception

A common misconception is:

```text
State Stores Every Previous Value
```

Usually, it does not.

State is primarily concerned with:

```text
The Latest Value
```

History may be stored elsewhere if an application requires it, but history is not what defines state.

---

## Putting Everything Together

State naturally has four important characteristics.

```text
One Current Value

↓

Changes Over Time

↓

Latest Value Matters

↓

Readers Expect The Latest Value
```

These characteristics appear repeatedly in Android applications:

```text
Music Player

Shopping Cart

Weather

Login Screen
```

regardless of how the state is implemented.

---

## The Key Mental Model

Imagine a scoreboard.

The numbers constantly change:

```text
120 / 2

↓

124 / 2

↓

130 / 3
```

But the scoreboard always displays:

```text
The Current Score
```

Applications behave in the same way.

State continuously evolves while always representing:

```text
The Current Situation
```

---

## Revision Table

| Section | Content |
|----------|----------|
| Core Idea | State represents the latest current situation |
| Characteristic 1 | One current value |
| Characteristic 2 | Changes over time |
| Characteristic 3 | Latest value is most important |
| Characteristic 4 | Readers expect the latest value |
| Mental Model | Cricket scoreboard |
| Android Examples | Login screen, Shopping cart |
| Previous Concept | State Holder |
| Next Concept | Why StateFlow Exists |
