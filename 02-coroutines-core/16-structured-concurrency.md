# Structured Concurrency

## What Problem Were We Facing?

In the previous chapter, we learned:

```text
Parent Job
↓
Child Jobs
```

This immediately raises an important question:

```text
Why Do Jobs Form A Hierarchy At All?
```

Why not simply have:

```text
Job A

Job B

Job C
```

with no relationship between them?

To answer that question, we need to understand:

```text
Structured Concurrency
```

---

## Abstraction Level

In this chapter we are discussing:

```text
Why Related Coroutines Should Stay Connected
```

We are NOT yet discussing:

```text
coroutineScope Builder

runBlocking

SupervisorJob
```

Those topics will come later.

For now, we are focused on:

```text
What Structured Concurrency Is

Why Kotlin Uses It
```

---

## First Intuition

Imagine loading a screen.

To display the screen, we may need:

```text
Load User

Load Feed

Load Notifications
```

These are separate tasks.

However:

```text
They All Belong To The Same Screen
```

The screen is not truly ready until all of them are finished.

Conceptually:

```text
Screen Loading
├─ Load User
├─ Load Feed
└─ Load Notifications
```

This is one logical operation composed of multiple smaller operations.

---

## The Problem With Independent Coroutines

Suppose these tasks are started as completely independent coroutines.

Conceptually:

```text
Coroutine A

Coroutine B

Coroutine C
```

Now consider this question:

```text
When Is The Screen Finished Loading?
```

The answer is not:

```text
When Coroutine A Finishes
```

or:

```text
When Coroutine B Finishes
```

The screen is finished only when:

```text
All Related Work Is Finished
```

Without relationships between coroutines, expressing this becomes difficult.

---

## The Core Idea

Structured Concurrency is based on a simple principle:

```text
Related Coroutines Should Stay Related
```

Instead of:

```text
Independent Coroutines
```

we organize them into:

```text
Parent
├─ Child
├─ Child
└─ Child
```

This allows multiple coroutines to be treated as one logical unit of work.

---

## Definition

Structured Concurrency is a design principle where related coroutines are organized into parent-child hierarchies.

Conceptually:

```text
One Logical Operation
├─ Coroutine A
├─ Coroutine B
└─ Coroutine C
```

rather than:

```text
Independent Coroutine

Independent Coroutine

Independent Coroutine
```

---

## Mental Model

Think of Structured Concurrency as:

```text
A Project
```

A project may contain:

```text
Task A

Task B

Task C
```

The project is considered complete only when:

```text
All Related Tasks Are Complete
```

Similarly:

```text
Parent
├─ Child
├─ Child
└─ Child
```

represents one larger operation composed of smaller pieces.

---

## Why Is This Useful?

Structured Concurrency allows Kotlin to represent:

```text
One Large Operation
```

using:

```text
Multiple Smaller Coroutines
```

while still treating them as a connected unit.

Conceptually:

```text
Load Screen
├─ Load User
├─ Load Feed
└─ Load Notifications
```

instead of:

```text
Random Coroutine

Random Coroutine

Random Coroutine
```

---

## Important Clarification

Structured Concurrency does NOT mean:

```text
Coroutines Run At The Same Time
```

This concept is not about:

```text
Threads

Parallelism

Performance
```

It is about:

```text
Relationships

Grouping

Logical Structure
```

---

## Another Important Clarification

Structured Concurrency is not:

```text
A Specific API
```

It is a design principle.

Later we will learn APIs that help implement this principle.

For now, focus on the idea itself.

---

## Why Is It Important?

Without Structured Concurrency:

```text
Coroutines Exist As Separate Pieces
```

With Structured Concurrency:

```text
Related Coroutines Form One Logical Unit
```

This makes complex asynchronous work easier to reason about.

---

## The Next Question

At this point a natural question appears:

```text
Okay...

How Do We Actually Create These Parent-Child Relationships?
```

This question leads us to:

```text
coroutineScope
```

which is one of Kotlin's primary tools for creating structured coroutine hierarchies.

---

## Common Misconceptions

### Misconception 1

```text
Structured Concurrency = Parallelism
```

Incorrect.

This concept is about relationships and organization.

---

### Misconception 2

```text
Structured Concurrency Is A Specific API
```

Incorrect.

It is a design principle.

---

### Misconception 3

```text
Structured Concurrency Requires Multiple Threads
```

Incorrect.

Thread usage is a separate concern.

---

## Relationship With Future Concepts

Structured Concurrency directly connects to:

```text
coroutineScope

runBlocking

SupervisorJob

Cancellation
```

These concepts build upon structured coroutine hierarchies.

---

## Revision Table

| Section | Content |
|----------|----------|
| Definition | Organizing related coroutines into parent-child hierarchies |
| Main Idea | Related coroutines should stay related |
| Main Benefit | Treat multiple coroutines as one logical operation |
| About Threads? | No |
| About Parallelism? | No |
| API? | No, design principle |
| Previous Concept | Parent Job & Child Job |
| Next Concept | coroutineScope Builder |

---

## Future Exploration

- coroutineScope
- runBlocking
- SupervisorJob
- Cancellation
