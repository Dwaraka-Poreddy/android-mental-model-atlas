# State Write Tracking

## Observation

In the previous chapter, we learned that Compose records which composables read which pieces of state.

For example,

```kotlin
Text("$count")
```

creates a dependency like this.

```text
Counter()

↓

Reads count
```

A simple question appears.

```text
What Actually Happens The Moment count Changes?
```

---

## A Thought Experiment

Imagine a librarian.

Earlier,

the librarian recorded:

```text
Alice

↓

Borrowed Math Book
```

Later,

the publisher releases a new edition of the Math book.

```text
Math Book

↓

New Edition
```

The librarian immediately notices that the book has changed.

Now,

the librarian knows it's time to notify everyone who borrowed it.

Notice that these are two different responsibilities.

```text
Read Tracking

↓

Who Borrowed The Book?

------------------------

Write Tracking

↓

The Book Changed
```

One records dependencies.

The other detects changes.

---

## Connecting It To Compose

Compose works in exactly the same way.

Earlier,

the Snapshot System recorded:

```text
Counter()

↓

Reads count
```

Later,

this happens.

```kotlin
count++
```

The Snapshot System immediately detects that:

```text
count Changed
```

This is called **State Write Tracking**.

---

## A New Mental Model

Think of the Snapshot System performing two different jobs.

```text
State Read Tracking

↓

Who Depends On This State?

------------------------

State Write Tracking

↓

This State Just Changed
```

Only after both pieces of information are available,

can Compose determine what needs to update.

---

## Why State Write Tracking Exists

Imagine Compose only knew:

```text
Counter() Depends On count
```

That alone isn't enough.

Compose also needs to know **when** `count` changes.

State Write Tracking provides that missing information.

```text
State Changed

↓

Compose Can React
```

---

## The Complete Picture

Everything we've learned now comes together.

```text
Composable Reads count

↓

State Read Tracking

↓

Dependency Recorded

=========================

count++

↓

State Write Tracking

↓

Change Detected

=========================

Compose Already Knows Who Needs To Update
```

Compose doesn't need to search for affected composables.

It already has everything it needs.

---

## What State Write Tracking Does NOT Do

State Write Tracking does **not**:

```text
Perform Recomposition

↓

Render The UI

↓

Store State
```

Its responsibility is simply:

```text
Detect State Changes
```

The recomposition process itself comes later.

---

## Production Recognition

When you write:

```kotlin
count++
```

don't think:

```text
Just Changing A Variable
```

think:

```text
Snapshot System Detected A State Change
```

---

## Revision

### Core Idea

```text
State Write Tracking

↓

Detect State Changes
```

### Mental Model

```text
State Read Tracking

↓

Who Uses The State?

------------------------

State Write Tracking

↓

When The State Changes
```

### Complete Picture

```text
Read State

↓

Dependency Recorded

↓

Write State

↓

Change Detected

↓

Compose Knows What Needs Attention
```

### Production Recognition

```kotlin
count++
```

↓

```text
Snapshot System Detected The Change
```

---

## Part 3 Summary

We've now uncovered how Compose observes state.

```text
Snapshot State

↓

Observable State

↓

State Read Tracking

↓

Record Dependencies

↓

State Write Tracking

↓

Detect Changes

↓

Compose Knows Exactly What Changed
```

Compose doesn't search for what needs updating.

It already knows:

- which composables depend on the state
- when that state changes

---

## One Remaining Question

Compose now knows:

- what state changed
- which composables depend on it

A natural question appears.

```text
How Does Compose Actually Perform Recomposition?
```

That leads us to the next part of the document.

```text
Recomposition Algorithm
```
