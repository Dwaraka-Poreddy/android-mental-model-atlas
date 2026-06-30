# Snapshot State

## Observation

In the previous chapter, we learned that the Snapshot System observes state changes.

A simple question appears.

```text
Does Compose Notice Every Variable Change?
```

Imagine this code.

```kotlin
var count = 0
```

Later,

we update it.

```kotlin
count++
```

Will Compose automatically update the UI?

The answer is:

```text
No.
```

A normal Kotlin variable isn't enough.

---

## A Thought Experiment

Imagine a security guard watching the entrance of a building.

Some doors have security sensors.

Others don't.

If someone enters through a door with a sensor,

the guard immediately notices.

```text
Door

↓

Security Sensor

↓

Guard Notices
```

But suppose someone enters through a window.

```text
Window

↓

No Sensor

↓

Guard Never Knows
```

The change still happened.

The guard simply wasn't watching it.

---

## Connecting It To Compose

Compose works in a very similar way.

It cannot observe every variable in your Kotlin program.

Instead,

it observes a special kind of state.

That state is called **Snapshot State**.

For example,

```kotlin
var count by remember {
    mutableStateOf(0)
}
```

The object created by:

```kotlin
mutableStateOf(...)
```

is Snapshot State.

Because it is observable,

the Snapshot System can detect when it changes.

---

## Connecting It To What We Already Learned

Earlier in the Atlas,

we learned that:

```text
mutableStateOf(...)

↓

Observable State
```

Now we can make that idea more precise.

```text
Observable State

↓

Observable By The Snapshot System
```

Every Snapshot State is observable state.

The Snapshot System is the component that observes it.

---

## A New Mental Model

Not every variable is observable.

```kotlin
var count = 0
```

↓

```text
Normal Variable

↓

Snapshot System Cannot Observe
```

---

```kotlin
var count by remember {
    mutableStateOf(0)
}
```

↓

```text
Snapshot State

↓

Snapshot System Can Observe
```

That's the important difference.

---

## Why Snapshot State Exists

The Snapshot System can only react to changes that it can observe.

Normal Kotlin variables don't provide that information.

Snapshot State does.

That's why changing:

```kotlin
count++
```

updates the UI **only** when `count` is Snapshot State.

---

## What Snapshot State Does NOT Do

Snapshot State does **not**:

```text
Perform Recomposition

↓

Render The UI

↓

Remember Values
```

Its responsibility is much simpler.

```text
Represent Observable State
```

The Snapshot System watches it.

Compose reacts to it.

---

## Production Recognition

When you see:

```kotlin
mutableStateOf(...)
```

don't think:

```text
Just Another State Holder
```

think:

```text
Observable State

↓

The Snapshot System Can Watch This
```

---

## Revision

### Core Idea

```text
Snapshot State

↓

Observable State
```

### Mental Model

```text
Normal Variable

↓

Not Observable

------------------------

Snapshot State

↓

Observable
```

### Connection To Doc 6

```text
mutableStateOf(...)

↓

Observable State

↓

Observed By The Snapshot System
```

### Production Recognition

```kotlin
mutableStateOf(...)
```

↓

```text
State The Snapshot System Can Observe
```

---

## One Remaining Question

The Snapshot System can observe Snapshot State.

A natural question appears.

```text
How Does Compose Know Which Composable Read That State?
```

That leads us to the next concept.

```text
State Read Tracking
```
