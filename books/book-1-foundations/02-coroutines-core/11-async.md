# async

## What Problem Were We Facing?

In the previous chapter, we learned:

```text
launch
↓
Create Coroutine
↓
Start Coroutine
↓
Return Job
```

This works well when our goal is simply:

```text
Perform Work
```

However, a natural question appears:

```text
What If I Need A Result Back?
```

For example:

```text
Download User
↓
Need User Object

Calculate Total
↓
Need Result

Load Configuration
↓
Need Configuration Data
```

In these situations, simply starting work is not enough.

We also need a way to obtain the result.

This is where:

```text
async
```

comes in.

---

## Abstraction Level

In this chapter we are discussing async from a developer's perspective.

We are NOT yet discussing:

```text
Dispatchers

Thread Selection

Exception Handling

Structured Concurrency
```

Those topics will come later.

For now, we are focused on:

```text
What async Does

Why It Exists

How It Differs From launch
```

---

## First Intuition

Suppose you want to calculate:

```text
Total Price
```

Starting the calculation is useful.

But eventually you need:

```text
The Answer
```

Similarly:

```text
Download User
```

is only useful if you eventually obtain:

```text
User Data
```

This is the problem async was designed to solve.

---

## Definition

async is a coroutine builder that creates and starts a coroutine which is expected to produce a result.

Conceptually:

```text
async
↓
Create Coroutine
↓
Perform Work
↓
Produce Result
```

---

## Mental Model

Think of:

```text
launch
```

as:

```text
Start Some Work
```

and:

```text
async
```

as:

```text
Start Some Work And Eventually Give Me A Result
```

---

## Basic Example

```kotlin
async {
    fetchUser()
}
```

Conceptually:

```text
async Called
↓
Coroutine Created
↓
Work Executes
↓
Result Produced
```

---

## How Is async Different From launch?

This is the most important comparison.

### launch

Usually used when:

```text
Work Matters

Result Does Not
```

Examples:

```text
Save Analytics

Write Logs

Refresh Cache
```

---

### async

Usually used when:

```text
Work Matters

Result Also Matters
```

Examples:

```text
Load User

Calculate Total

Fetch Product List
```

---

## Can We Interact With The Coroutine Later?

Earlier we learned:

```text
launch
↓
Returns Job
```

A natural question is:

```text
What Does async Return?
```

The answer is:

```text
Deferred
```

Conceptually:

```text
async
↓
Create Coroutine
↓
Return Deferred
```

---

## What Is Deferred?

For now, think of:

```text
Deferred
```

as:

```text
A Promise That A Result Will Arrive Later
```

The coroutine may still be running.

The result may not yet be available.

However:

```text
Deferred
```

represents that future result.

---

## Mental Model For Deferred

Imagine ordering food.

You receive:

```text
Order Token
```

The food is not ready yet.

However:

```text
Order Token
↓
Represents Future Food
```

Similarly:

```text
Deferred
↓
Represents Future Result
```

The actual result may arrive later.

---

## launch vs async

A useful mental model is:

| Builder | Returns |
|----------|----------|
| launch | Job |
| async | Deferred |

Conceptually:

```text
launch
↓
I Care About The Work

async
↓
I Care About The Result
```

---

## Important Clarification

A common misconception:

```text
async
↓
Immediately Gives Result
```

Incorrect.

The result may not be ready yet.

That is why async returns:

```text
Deferred
```

instead of the actual result.

---

## Another Important Clarification

A common misconception:

```text
async
↓
Runs On Background Thread
```

Incorrect.

Like launch:

```text
async
```

creates a coroutine.

Thread selection is a separate topic.

---

## Why Is async Important?

Many real-world operations need results.

Examples:

```text
Fetch User

Load Products

Calculate Values

Retrieve Configuration
```

async provides a way to:

```text
Start Work
↓
Obtain Result Later
```

using coroutines.

---

## Looking Back

Notice how our mental model is evolving:

```text
Coroutine Builder
↓
Creates Coroutine

launch
↓
Create Coroutine
↓
Return Job

async
↓
Create Coroutine
↓
Return Deferred
```

---

## The Next Question

At this point a natural question appears:

```text
Okay...

If async Returns Deferred, How Do I Get The Actual Result?
```

This question leads us to:

```text
await
```

which is responsible for obtaining the result from a Deferred.

---

## Common Misconceptions

### Misconception 1

```text
async = launch
```

Not exactly.

They are different coroutine builders.

---

### Misconception 2

```text
async Immediately Returns Result
```

Incorrect.

It returns Deferred.

---

### Misconception 3

```text
Deferred = Actual Result
```

Incorrect.

Deferred represents a future result.

---

### Misconception 4

```text
async Runs On Background Thread
```

Incorrect.

Thread selection is separate.

---

## Relationship With Future Concepts

async directly connects to:

```text
Deferred

await

Job

Dispatchers
```

These concepts explain how results are retrieved and managed.

---

## Revision Table

| Section | Content |
|----------|----------|
| Definition | Coroutine builder for work that produces a result |
| Returns | Deferred |
| Main Purpose | Obtain result later |
| Same As launch? | No |
| Immediate Result? | No |
| Abstraction Level | Coroutine Builder |
| Previous Concept | launch |
| Next Concept | await |
| Related Concepts | Deferred, Job |

---

## Future Exploration

- await
- Deferred
- Job
- Dispatchers