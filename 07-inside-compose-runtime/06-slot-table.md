# Slot Table

## Observation

Earlier, we learned that `remember` preserves a value across recompositions.

For example:

```kotlin
var count by remember {
    mutableStateOf(0)
}
```

When the composable recomposes,

the value is still available.

A simple question appears.

```text
Where Does Compose Actually Store This Value?
```

---

## A Thought Experiment

Imagine a teacher taking attendance.

As students enter the classroom,

the teacher records them.

```text
Seat 1

↓

Alice

------------------------

Seat 2

↓

Bob

------------------------

Seat 3

↓

Charlie
```

The teacher now has a record of:

- who is present
- where each student belongs

The next day,

the students enter the classroom again.

Instead of creating a completely new attendance sheet,

the teacher uses the existing one to recognize everyone.

The attendance sheet becomes the teacher's memory.

---

## Connecting It To Compose

Compose faces a similar problem.

Every time your composable functions run,

Compose needs somewhere to remember information from previous compositions.

For example,

it needs to remember:

- values created with `remember`
- state associated with composables
- information needed for future recompositions

That place is called the **Slot Table**.

---

## A New Mental Model

Think of the Slot Table as the runtime's memory.

```text
Composable Functions

↓

Composer

↓

Slot Table

↓

Everything Compose Needs To Remember
```

Whenever Compose needs information from a previous composition,

it retrieves it from the Slot Table.

---

## The Slot Table Is NOT The Composition Tree

At first,

it might seem like the Composition Tree could store all of this information.

But the two have different responsibilities.

```text
Composition Tree

↓

Understand The UI

------------------------

Slot Table

↓

Remember Information About The UI
```

One helps Compose understand your composable hierarchy.

The other helps Compose remember information across recompositions.

---

## What The Slot Table Does NOT Do

The Slot Table does **not**:

```text
Draw The UI

↓

Render Pixels
```

It simply stores the information the runtime needs to remember.

We'll learn exactly **what** is stored there in the upcoming chapters.

---

## Production Recognition

When you see:

```kotlin
remember {
    ...
}
```

don't think:

```text
Compose Magically Remembers
```

think:

```text
Compose Stores This Information Inside The Slot Table
```

---

## Revision

### Core Idea

```text
Slot Table

↓

Compose's Memory
```

### Mental Model

```text
Composable Functions

↓

Composer

↓

Slot Table

↓

Remembered Information
```

### Responsibilities

```text
Composition Tree

↓

Understand The UI

------------------------

Slot Table

↓

Remember Information
```

### Production Recognition

```kotlin
remember { ... }
```

↓

```text
Stored Inside The Slot Table
```

---

## One Remaining Question

We now know that Compose stores information inside the Slot Table.

A natural question appears.

```text
How Does The Slot Table Organize All This Information?
```

That leads us to the next concept.

```text
Groups
```
