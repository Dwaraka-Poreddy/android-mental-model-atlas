# Stability

## Observation

In the previous chapter,

we learned that Compose compares a composable's inputs before deciding whether to skip recomposition.

```text
Reached Composable

↓

Compare Inputs

↓

Skip or Recompose
```

A natural question appears.

```text
Compose compares inputs.

↓

But how does it know those comparisons can be trusted?
```

---

## A Thought Experiment

Imagine someone hands you a folder and asks:

> "Has anything inside this folder changed since yesterday?"

If the folder contains only one sheet of paper,

it's easy to compare.

Now imagine the folder contains hundreds of papers,

photos,

receipts,

and sticky notes.

Even if the folder looks the same,

you can't confidently assume nothing inside it changed.

Sometimes,

you simply don't have enough information to trust the comparison.

---

## Connecting It To Compose

Compose faces the same challenge.

Imagine this composable.

```kotlin
Header(user)
```

Before skipping recomposition,

Compose needs confidence that `user` hasn't meaningfully changed.

For simple values like:

```kotlin
Int
String
Boolean
```

this is straightforward.

For larger objects,

it's much harder.

Compose needs to know whether comparing that object is reliable.

That idea is called **Stability**.

---

## A New Mental Model

Think of Stability as a promise.

```text
Stable Type

↓

Safe To Compare

↓

Safe To Reuse
```

If that promise holds,

Compose can confidently skip recomposition when nothing has changed.

---

## Why Stability Exists

Imagine Compose couldn't trust its comparisons.

Whenever it wasn't sure whether an object had changed,

it would have to recompose the composable just to be safe.

Stability allows Compose to safely avoid unnecessary work.

---

## What Stability Does NOT Mean

Stability does **not** mean:

```text
The Object Can Never Change
```

Instead,

it means Compose can reliably determine whether a meaningful change has occurred.

That's all it needs to make safe recomposition decisions.

---

## Production Recognition

When you hear:

```text
Stable Type
```

don't think:

```text
Just Another Compose Term
```

think:

```text
A type that Compose can safely compare when deciding whether to skip recomposition.
```

---

## Revision

### Core Idea

```text
Stability

↓

Reliable Comparisons
```

### Mental Model

```text
Stable Type

↓

Safe To Compare

↓

Safe To Skip
```

### Why It Exists

```text
Trust Comparisons

↓

Avoid Unnecessary Recomposition
```

### Production Recognition

```text
Stable Type
```

↓

```text
Compose can safely trust comparisons.
```

---

## One Remaining Question

Compose can safely skip recomposition only if it knows a type is stable.

A natural question appears.

```text
How does Compose determine whether a type is stable?
```

That leads us to the next concept.

```text
Stability Inference
```
