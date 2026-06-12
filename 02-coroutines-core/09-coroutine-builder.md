# Coroutine Builder

## What Problem Were We Facing?

In previous chapters, we learned:

```text
Coroutine
↓
Suspendable Work

Suspension
↓
Pause

Resume
↓
Continue Later

Continuation
↓
Remembers How To Continue
```

At this point a natural question appears:

```text
How Do We Create A Coroutine?
```

A coroutine does not appear automatically.

Something must create it.

That something is known as a:

```text
Coroutine Builder
```

---

## Abstraction Level

In this chapter we are discussing coroutine creation from a developer's perspective.

We are NOT yet discussing:

```text
Dispatchers

Thread Selection

Coroutine Internals
```

Those topics will come later.

For now, we are focused on:

```text
What A Coroutine Builder Is

Why It Exists
```

---

## First Intuition

Imagine building a house.

Before a house exists:

```text
House
↓
Does Not Exist
```

Someone must build it.

Similarly:

```text
Coroutine
↓
Does Not Exist
```

until something creates it.

That creator is called a:

```text
Coroutine Builder
```

---

## Definition

A Coroutine Builder is a construct that creates and starts a coroutine.

Conceptually:

```text
Coroutine Builder
↓
Creates Coroutine
↓
Coroutine Begins Execution
```

---

## Why Does Kotlin Need Coroutine Builders?

Earlier we learned:

```text
Coroutine
↓
Represents Suspendable Work
```

However:

```text
Representing Work
```

and

```text
Starting Work
```

are different responsibilities.

Something must be responsible for:

```text
Creating

Starting

Managing Initial Setup
```

of a coroutine.

That responsibility belongs to:

```text
Coroutine Builders
```

---

## Mental Model

Think of a Coroutine Builder as:

```text
A Factory
```

Conceptually:

```text
Factory
↓
Creates Product

Coroutine Builder
↓
Creates Coroutine
```

The builder itself is not the coroutine.

It creates the coroutine.

---

## Common Coroutine Builders

Some common coroutine builders are:

```text
launch

async

runBlocking
```

We will study each of them separately.

For now, it is enough to know:

```text
These Are Coroutine Builders
```

---

## Builder vs Coroutine

This distinction is important.

A common misconception:

```text
launch = Coroutine
```

Incorrect.

More accurately:

```text
launch
↓
Coroutine Builder

Coroutine Builder
↓
Creates Coroutine
```

The builder and the coroutine are different things.

---

## Another Mental Model

Imagine pressing:

```text
Play
```

on a video player.

The button itself is not:

```text
The Movie
```

Instead:

```text
Button
↓
Starts Movie
```

Similarly:

```text
Coroutine Builder
↓
Starts Coroutine
```

---

## Why Is This Important?

Without Coroutine Builders:

```text
Coroutine
```

would remain only a concept.

Coroutine Builders provide the mechanism for:

```text
Creating

Starting

Running
```

coroutines.

---

## Looking Back

Notice how our mental model has evolved:

```text
Coroutine
↓
Suspendable Work

Suspension
↓
Pause

Resume
↓
Continue Later

Continuation
↓
Remember How To Continue

Coroutine Builder
↓
Create And Start Coroutine
```

---

## The Next Question

At this point a natural question appears:

```text
Okay...
What Is launch?
```

This leads us to the first and most commonly used coroutine builder:

```text
launch
```

which we will study next.

---

## Common Misconceptions

### Misconception 1

```text
Coroutine Builder = Coroutine
```

Incorrect.

The builder creates the coroutine.

---

### Misconception 2

```text
launch = Coroutine
```

Incorrect.

launch is a coroutine builder.

---

### Misconception 3

```text
Coroutine Starts Automatically
```

Incorrect.

Something must create it.

---

### Misconception 4

```text
Every Builder Behaves The Same
```

Incorrect.

Different builders have different purposes.

---

## Relationship With Future Concepts

Coroutine Builder directly connects to:

```text
launch

async

runBlocking
```

These are specific coroutine builders provided by Kotlin.

---

## Revision Table

| Section | Content |
|----------|----------|
| Definition | Creates and starts a coroutine |
| Mental Model | Factory |
| Same As Coroutine? | No |
| Common Examples | launch, async, runBlocking |
| Main Purpose | Coroutine creation |
| Abstraction Level | Coroutine Creation |
| Previous Concept | Continuation |
| Next Concept | launch |
| Related Concepts | async, runBlocking |

---

## Future Exploration

- launch
- async
- runBlocking