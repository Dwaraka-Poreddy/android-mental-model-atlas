# Stability

## Observation

In the previous chapter,

we learned that when recomposition reaches a composable,

Compose doesn't immediately execute it.

Instead,

it first compares its inputs.

```text
Reached Composable

↓

Compare Inputs

↓

Skip or Recompose
```

A natural question appears.

```text
Suppose Compose Cannot Trust One Of Those Inputs.

↓

What Happens?
```

---

## The Answer

Compose always chooses correctness over performance.

If it isn't confident that an input can be safely compared,

it plays it safe.

```text
Can't Trust The Comparison

↓

Don't Skip

↓

Recompose
```

Even if nothing actually changed,

Compose would rather perform an unnecessary recomposition

than accidentally skip one that was required.

---

## A Thought Experiment

Imagine someone asks:

> "Has anything inside this folder changed since yesterday?"

If the folder contains a single sheet of paper,

it's easy to answer.

Now imagine the folder contains hundreds of papers,

receipts,

photos,

and sticky notes.

Even if the folder itself looks identical,

you can't confidently say that nothing inside changed.

When you're unsure,

the safest decision is to inspect it again.

Compose follows exactly the same principle.

---

## Connecting It To Compose

Imagine this composable.

```kotlin
Header(user)
```

Suppose yesterday,

`user` looked like this.

```kotlin
User(
    name = "John",
    age = 25
)
```

Today,

someone changes:

```kotlin
user.age++
```

The object is still the same object,

but something inside it changed.

Compose now has a problem.

```text
Can I Trust That Comparing This Object Is Enough?
```

If the answer is **No**,

Compose plays it safe.

It recomposes.

---

## This Is What Stability Means

A **stable** type is simply a type that Compose trusts when making recomposition decisions.

Think of it like this.

```text
Stable Type

↓

Compose Can Trust It

↓

Safe To Skip If Nothing Changed
```

An **unstable** type is the opposite.

```text
Unstable Type

↓

Compose Cannot Trust It

↓

Play It Safe

↓

Recompose
```

Notice something important.

Compose isn't saying the object is "bad."

It's simply saying:

> "I can't safely optimize around this object."

---

## How Does Compose Decide?

A natural question appears.

```text
How Does Compose Know Whether A Type Is Stable?
```

Most of the time,

Compose figures it out automatically.

It analyzes your types and looks for clues such as:

- Are the properties immutable (`val`)?
- Are the properties themselves stable?
- Does the type obey Compose's stability rules?

This process is called **Stability Inference**.

Most developers never need to think about it.

---

## How Do I Know If Compose Thinks My Type Is Unstable?

As a developer,

you usually don't guess.

You discover it when:

- Android Studio warns about unstable parameters.
- Compose compiler reports mark a type as unstable.
- You investigate unnecessary recompositions.
- Performance profiling shows more recompositions than expected.

In other words,

this usually becomes important during performance optimization,

not while building your first Compose screen.

---

## Can I Help Compose?

Yes.

Most of the time, Compose can determine stability automatically.

When it can't, there are several ways developers can help.

### Prefer immutable models

Instead of:

```kotlin
class User(
    var name: String
)
```

prefer:

```kotlin
data class User(
    val name: String
)
```

Immutable models are much easier for Compose to reason about.

---

### Use immutable collections

Prefer immutable collections where appropriate instead of mutable ones.

---

### Advanced Stability Tools

Compose also provides advanced mechanisms such as:

- `@Immutable`
- `@Stable`

These allow developers to explicitly communicate stability information to the Compose Runtime when automatic inference isn't sufficient.

We're intentionally **not** covering them in this chapter.

To understand when to use them, how they differ, the contracts they impose, and the common mistakes to avoid, we'll study them in detail in **Doc 8 — Production Compose Patterns**, where they naturally fit alongside other performance optimization techniques.

For now,

it's enough to know that these tools exist and that they allow developers to provide additional stability information when necessary.

---

## The Bigger Picture

Stability is not about making your code "correct."

Your UI works perfectly even with unstable types.

Stability is about helping Compose perform fewer unnecessary recompositions.

```text
Stable Types

↓

Better Opportunities To Skip Recomposition

↓

Better Performance
```

Correctness comes first.

Performance comes second.

Compose never sacrifices correctness just to skip more work.

---

## Production Recognition

When you hear:

```text
Stable Type
```

don't think:

```text
Just Another Compose Optimization
```

think:

```text
A Type Compose Can Trust When Deciding Whether To Skip Recomposition.
```

---

## Revision

### Core Idea

```text
Stability

↓

Trust Comparisons
```

### Mental Model

```text
Stable

↓

Trust

↓

Possible To Skip

------------------------

Unstable

↓

Don't Trust

↓

Play It Safe

↓

Recompose
```

### Why It Matters

```text
Better Stability

↓

More Safe Skipping

↓

Better Performance
```

### Developer Perspective

```text
Compose Can't Trust My Type

↓

Can I Help?

↓

Usually Yes
```

---

## One Remaining Question

We've now learned how Compose decides whether it can safely skip recomposition.

A natural question appears.

```text
After Recomposition Finishes,

↓

Where Does The Updated UI Actually Live?
```

That leads us to the next part of the document.

```text
LayoutNode
```
