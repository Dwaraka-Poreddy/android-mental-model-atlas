# Keys

## Observation

In the previous chapter, we learned how Compose remembers values across recompositions.

A simple question appears.

```text
What Happens If The Structure Of The UI Changes?
```

Imagine this composable.

```kotlin
Column {

    Header()

    Content()

    Footer()

}
```

Now suppose the code changes.

```kotlin
Column {

    Banner()

    Header()

    Content()

    Footer()

}
```

A simple question appears.

```text
Header()

Still Exists.

↓

How Does Compose Know This Is The Same Header?
```

---

## A Thought Experiment

Imagine a classroom.

Initially,

students sit like this.

```text
Seat 1 → Alice

Seat 2 → Bob

Seat 3 → Charlie
```

The next day,

a new student joins the class.

```text
Seat 1 → David

Seat 2 → Alice

Seat 3 → Bob

Seat 4 → Charlie
```

If the teacher identifies students only by seat number,

everything becomes confused.

Alice suddenly looks like David.

Bob looks like Alice.

Charlie looks like Bob.

The seats changed.

The students didn't.

The teacher needs a better way to recognize each student.

---

## Connecting It To Compose

Compose faces the same challenge.

Originally,

it sees:

```kotlin
Column {

    Header()

    Content()

    Footer()

}
```

Later,

the UI changes.

```kotlin
Column {

    Banner()

    Header()

    Content()

    Footer()

}
```

The composables still exist,

but their positions have changed.

Compose now needs to answer a very important question.

```text
Is This Still The Same Composable?
```

---

## The Solution

Compose uses **Keys** to identify composables.

Sometimes,

their position is enough.

But when composables can move,

developers can provide an explicit key.

```kotlin
key(user.id) {

    UserCard(user)

}
```

Now Compose knows:

```text
Even Though The Position Changed,

↓

This Is Still The Same Composable.
```

---

## A New Mental Model

Keys don't help Compose remember values.

Compose already knows how to do that.

Instead,

Keys help Compose remember **which composable those values belong to.**

Think of it like this.

```text
remember

↓

Remember The Value

------------------------

Keys

↓

Remember The Identity
```

The two solve different problems.

---

## Why Keys Matter

Imagine a composable remembers some local state.

```kotlin
var expanded by remember {
    mutableStateOf(false)
}
```

If the composable moves,

Compose needs to know whether that remembered state should move with it,

or belong to a different composable.

Keys provide that identity.

Without them,

Compose may associate remembered state with the wrong composable.

---

## What Keys Do NOT Do

Keys do **not**:

```text
Store Values

↓

Trigger Recomposition

↓

Render The UI
```

Their responsibility is much simpler.

```text
Help Compose Recognize The Same Composable Across Changes
```

---

## Production Recognition

When you see:

```kotlin
key(user.id) {

    UserCard(user)

}
```

don't think:

```text
Random Compose Syntax
```

think:

```text
Compose, This Is Still The Same Composable, Even If It Moved.
```

---

## Revision

### Core Idea

```text
Keys

↓

Remember Identity
```

### Mental Model

```text
remember

↓

Remember The Value

------------------------

Keys

↓

Remember The Identity
```

### Responsibilities

```text
Slot Table

↓

Store Information

------------------------

Groups

↓

Organize Information

------------------------

Anchors

↓

Locate Information

------------------------

Keys

↓

Preserve Identity
```

### Production Recognition

```kotlin
key(user.id) { ... }
```

↓

```text
This Is Still The Same Composable
```

---

## Part 2 Summary

We've now uncovered how Compose remembers information across recompositions.

```text
remember

↓

Requests A Remembered Value

↓

Slot Table Stores It

↓

Groups Organize It

↓

Anchors Find It Again

↓

Keys Preserve Its Identity
```

The "magic" behind `remember` is no longer magic.

It's the result of several runtime components working together.

---

## One Remaining Question

Compose can now:

- remember values
- organize them
- find them again
- preserve their identity

A natural question appears.

```text
How Does Compose Know When Any Of Those Values Have Changed?
```

That leads us to the next part of the document.

```text
Snapshot System
```
