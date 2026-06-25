# Snapshot System

## Observation

Earlier, we learned that Compose can remember values across recompositions.

For example:

```kotlin
var count by remember {
    mutableStateOf(0)
}

Button(
    onClick = {
        count++
    }
)
```

When:

```kotlin
count++
```

executes,

the UI updates automatically.

A simple question appears.

```text
How Did Compose Even Notice That count Changed?
```

No one manually tells Compose to update the UI.

Yet,

the UI changes automatically.

How?

---

## A Thought Experiment

Imagine a security guard watching the entrance of a building.

People continuously enter and leave.

The guard doesn't repeatedly ask:

```text
Did Anyone Enter?

↓

Did Anyone Leave?

↓

Did Anyone Enter?
```

Instead,

the guard simply watches the entrance.

The moment someone walks through the door,

the guard immediately notices.

```text
Door Changes

↓

Guard Notices
```

The guard doesn't cause the change.

The guard simply observes it.

---

## Connecting It To Compose

Compose works in a very similar way.

Whenever observable state changes,

the runtime notices that change.

The component responsible for observing these changes is called the **Snapshot System**.

Think of it like this.

```text
Observable State Changes

↓

Snapshot System Notices

↓

Compose Can React
```

The Snapshot System doesn't update the UI itself.

Its responsibility is simply to detect that state has changed.

---

## A New Mental Model

Think of the Snapshot System as the runtime's observer.

```text
State Changes

↓

Snapshot System Observes

↓

Compose Knows Something Changed
```

Only after Compose knows that something changed,

can it decide what to update.

---

## Why The Snapshot System Exists

Imagine Compose had no way to observe state changes.

Every frame,

it would have to repeatedly ask:

```text
Did Anything Change?

↓

Did Anything Change?

↓

Did Anything Change?
```

That would be extremely inefficient.

Instead,

the Snapshot System immediately informs the runtime whenever observable state changes.

Compose reacts only when necessary.

---

## What The Snapshot System Does NOT Do

The Snapshot System does **not**:

```text
Remember Values

↓

Render The UI

↓

Perform Recomposition
```

Its responsibility is much simpler.

```text
Observe State Changes
```

We'll see in the next chapters exactly **what** it observes and **how** it knows which composables are affected.

---

## Production Recognition

When you write:

```kotlin
count++
```

don't think:

```text
Compose Magically Knows
```

think:

```text
State Changed

↓

Snapshot System Noticed

↓

Compose Can React
```

---

## Revision

### Core Idea

```text
Snapshot System

↓

Observes State Changes
```

### Mental Model

```text
State Changes

↓

Snapshot System

↓

Compose Knows Something Changed
```

### Responsibilities

```text
Slot Table

↓

Remember Information

------------------------

Keys

↓

Preserve Identity

------------------------

Snapshot System

↓

Observe State Changes
```

### Production Recognition

```kotlin
count++
```

↓

```text
State Changed

↓

Snapshot System Observed The Change
```

---

## One Remaining Question

The Snapshot System can observe state changes.

A natural question appears.

```text
What Exactly Can The Snapshot System Observe?
```

That leads us to the next concept.

```text
SnapshotState
```
