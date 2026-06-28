# Parent Job & Child Job

## What Problem Were We Facing?

In the previous chapter, we learned:

```text
CoroutineScope
↓
Owns Coroutines

Job
↓
Represents Lifecycle
```

This raises an important question:

```text
What Happens When A Scope Owns Multiple Coroutines?
```

For example:

```text
Coroutine A

Coroutine B

Coroutine C
```

Each coroutine has its own:

```text
Job
```

Are these Jobs completely independent?

Or do they have relationships?

The answer is:

```text
Yes

Jobs Can Form A Hierarchy
```

---

## Abstraction Level

In this chapter we are discussing:

```text
Job Relationships
```

We are NOT yet discussing:

```text
Cancellation Propagation

Exception Propagation

SupervisorJob
```

Those topics will come later.

For now, we are focused on:

```text
Parent Job

Child Job

Job Hierarchy
```

---

## First Intuition

Imagine a company.

```text
Manager
├─ Employee A
├─ Employee B
└─ Employee C
```

The employees are separate people.

However:

```text
They Belong To The Same Hierarchy
```

A similar idea exists with Jobs.

---

## The Core Idea

Suppose a scope creates:

```kotlin
launch {
    ...
}

launch {
    ...
}

launch {
    ...
}
```

Conceptually:

```text
Scope
├─ Coroutine A
├─ Coroutine B
└─ Coroutine C
```

Since each coroutine has a Job:

```text
Scope
├─ Job A
├─ Job B
└─ Job C
```

These Jobs are not isolated.

They become part of a hierarchy.

---

## Parent Job

A Parent Job is a Job that has one or more child Jobs beneath it.

Conceptually:

```text
Parent Job
├─ Child Job A
├─ Child Job B
└─ Child Job C
```

Think of the Parent Job as:

```text
The Root Of A Job Hierarchy
```

---

## Child Job

A Child Job is a Job that belongs to a Parent Job.

Conceptually:

```text
Parent Job
└─ Child Job
```

The Child Job still represents:

```text
Its Own Coroutine Lifecycle
```

However, it is now part of a larger hierarchy.

---

## Mental Model

Think of:

```text
Parent Job
```

as:

```text
A Tree Node
```

and:

```text
Child Jobs
```

as:

```text
Connected Nodes
```

Conceptually:

```text
Parent Job
├─ Child Job
├─ Child Job
└─ Child Job
```

The important idea is:

```text
Jobs Can Be Related
```

rather than existing independently.

---

## Why Does Kotlin Need This?

Imagine an application screen.

That screen may create:

```text
Load User

Load Feed

Load Notifications
```

as separate coroutines.

Conceptually:

```text
Screen
├─ Coroutine A
├─ Coroutine B
└─ Coroutine C
```

These operations are related.

They belong to the same logical unit of work.

A Job hierarchy allows Kotlin to represent these relationships.

---

## Important Clarification

A common misconception:

```text
Parent Job Performs Work
```

Incorrect.

Remember:

```text
Coroutine
↓
Performs Work

Job
↓
Represents Lifecycle
```

The Parent Job simply participates in the lifecycle hierarchy.

---

## Another Important Clarification

A common misconception:

```text
Parent Job = CoroutineScope
```

Incorrect.

A CoroutineScope provides ownership.

A Parent Job represents a position within a Job hierarchy.

These concepts are related but different.

---

## What Have We Learned?

We started with:

```text
Individual Jobs
```

Now we have:

```text
Job Hierarchy

Parent Job
↓
Child Jobs
```

This is the first step toward understanding how groups of coroutines are managed together.

---

## The Next Question

At this point a natural question appears:

```text
Okay...

Why Does Kotlin Need Job Hierarchies?
```

Or:

```text
What Benefits Do We Get From Connecting Jobs Together?
```

This question leads us to:

```text
Structured Concurrency
```

which is one of the most important ideas in Kotlin Coroutines.

---

## Common Misconceptions

### Misconception 1

```text
Parent Job Performs Work
```

Incorrect.

Coroutines perform work.

Jobs represent lifecycle.

---

### Misconception 2

```text
Child Job Is A Different Kind Of Job
```

Incorrect.

A Child Job is still a Job.

It simply has a Parent Job.

---

### Misconception 3

```text
Jobs Are Always Independent
```

Incorrect.

Jobs can form hierarchies.

---

## Relationship With Future Concepts

Parent Job and Child Job directly connect to:

```text
Structured Concurrency

Cancellation

SupervisorJob
```

These concepts build upon Job hierarchies.

---

## Revision Table

| Section | Content |
|----------|----------|
| Parent Job | Job with child Jobs |
| Child Job | Job belonging to a Parent Job |
| Main Idea | Jobs can form hierarchies |
| Performs Work? | No |
| Same As CoroutineScope? | No |
| Previous Concept | Job |
| Next Concept | Structured Concurrency |

---

## Future Exploration

- Structured Concurrency
- Cancellation
- SupervisorJob