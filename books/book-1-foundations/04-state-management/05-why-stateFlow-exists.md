# Why StateFlow Exists

## Looking Back

In the previous document we learned that:

```text
Flow

↓

Produces Values

↓

Collectors Receive Them
```

For example:

```text
1

↓

2

↓

3

↓

4
```

Every time a new value is emitted, collectors receive it.

Flow is excellent at delivering:

```text
New Values
```

---

## A New Observation

Consider a shopping cart.

Its current state is:

```text
Items: 3

Total: ₹1250
```

Suppose this information is exposed using a Flow.

```kotlin
fun observeCart(): Flow<CartState>
```

Now imagine the cart remains unchanged for the next ten minutes.

No new values are emitted.

During those ten minutes, another part of the application wants to display the current cart.

A natural question appears:

```text
What Value Can It Read Right Now?
```

---

## Following The Flow

The last emitted value was:

```text
Items: 3

Total: ₹1250
```

But after that:

```text
No New Values Were Emitted
```

From everything we learned about Flow, collectors receive values:

```text
When Values Are Emitted
```

If nothing new is emitted:

```text
Nothing New Can Be Collected
```

---

## The Problem

The cart still exists.

Its current state is still:

```text
Items: 3

Total: ₹1250
```

But a new collector has no way to immediately know that value.

It can only wait until something changes.

For example:

```text
Items: 4

Total: ₹1600
```

Only then does it receive a value.

---

## Another Example

Imagine a music player.

Current state:

```text
Song: Believer

Playing: true

Progress: 01:42
```

A mini player appears at the bottom of the screen.

The song is still playing.

The progress is still:

```text
01:42
```

Nothing has changed.

No new value has been emitted.

Should the mini player display:

```text
Nothing
```

until the progress becomes:

```text
01:43
```

Of course not.

It should immediately display:

```text
Believer

Playing

01:42
```

because that is still the current state.

---

## Another Mental Model

Imagine a digital notice board.

The board currently displays:

```text
Lunch Break

1:00 PM
```

A new employee walks by.

Should the board say:

```text
Wait Until The Next Announcement
```

No.

It should immediately show:

```text
Lunch Break

1:00 PM
```

Later the message changes to:

```text
Team Meeting

2:00 PM
```

Now everyone sees the updated information.

The notice board always provides:

```text
Current Information

+

Future Updates
```

---

## A Missing Piece

Flow naturally provides:

```text
Future Updates
```

State, however, has different requirements.

From the previous chapters we learned that state:

```text
Always Has One Current Value
```

and

```text
Readers Expect The Latest Value
```

Therefore representing state requires:

```text
Current Value

+

Future Updates
```

not just:

```text
Future Updates
```

---

## Why StateFlow Exists

StateFlow was created to represent:

```text
State
```

It allows consumers to immediately know:

```text
The Current State
```

while continuing to receive:

```text
Future State Updates
```

Conceptually:

```text
Flow

↓

Future Updates

-------------------------

StateFlow

↓

Current Value

+

Future Updates
```

---

## Android Example

Suppose a shopping cart is represented as:

```kotlin
data class CartState(

    val itemCount: Int,

    val totalPrice: Double
)
```

Different parts of the application may start observing the cart at different times.

Every observer expects:

```text
The Current Cart
```

immediately,

while also staying updated when the cart changes.

This is exactly the kind of problem StateFlow is designed to solve.

---

## Another Common Misconception

A common misconception is:

```text
Flow And StateFlow Solve The Same Problem
```

They do not.

Flow focuses on:

```text
Delivering New Values
```

StateFlow focuses on:

```text
Representing The Current State While Continuing To Deliver New Values
```

---

## The Key Mental Model

Think of the difference like this:

```text
Flow

↓

"What Changed Next?"

-------------------------

StateFlow

↓

"What Is True Right Now, And What Changes Next?"
```

---

## Revision Table

| Section | Content |
|----------|----------|
| Previous Knowledge | Flow delivers emitted values |
| New Problem | State consumers need the current value immediately |
| Mental Model | Digital notice board |
| Flow | Future updates |
| StateFlow | Current value + future updates |
| Core Motivation | Representing state requires more than future updates |
| Previous Concept | State vs Event |
| Next Concept | StateFlow |
