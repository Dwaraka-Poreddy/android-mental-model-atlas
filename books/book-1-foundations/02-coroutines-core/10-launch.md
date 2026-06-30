# launch

## What Problem Were We Facing?

In the previous chapter, we learned:

```text
Coroutine Builder
↓
Creates And Starts A Coroutine
```

We also learned that:

```text
launch
```

is one of Kotlin's coroutine builders.

This immediately raises an important question:

```text
What Exactly Does launch Do?
```

Understanding launch is important because it is one of the most commonly used ways to start a coroutine.

---

## Abstraction Level

In this chapter we are discussing launch from a developer's perspective.

We are NOT yet discussing:

```text
Dispatchers

Thread Selection

Structured Concurrency

Exception Propagation
```

Those topics will come later.

For now, we are focused on:

```text
What launch Does

When It Is Used

What It Returns
```

---

## First Intuition

Suppose you want to perform some work:

```text
Load User

Save Data

Fetch Feed
```

A coroutine can represent that work.

However:

```text
Coroutine
↓
Does Not Exist Yet
```

Something must create it.

One way to do that is:

```kotlin
launch {
    ...
}
```

---

## Definition

launch is a coroutine builder that creates and starts a new coroutine.

Conceptually:

```text
launch
↓
Create Coroutine
↓
Start Coroutine
```

---

## Mental Model

Think of:

```text
launch
```

as:

```text
Start This Work
```

Conceptually:

```text
Work To Do
↓
launch
↓
Coroutine Starts
```

---

## Basic Example

```kotlin
launch {
    println("Hello Coroutine")
}
```

Conceptually:

```text
launch Called
↓
Coroutine Created
↓
Coroutine Starts
↓
Work Executes
```

---

## Why Is It Called launch?

The name:

```text
launch
```

was chosen because it starts something.

Similar to:

```text
Launch Rocket
↓
Rocket Starts

launch Coroutine
↓
Coroutine Starts
```

The builder is responsible for getting the coroutine moving.

---

## Can We Interact With The Coroutine Later?

Suppose:

```kotlin
launch {
    ...
}
```

The coroutine has been created and started.

A natural question is:

```text
After launch Creates The Coroutine,

Do We Still Have Any Way To Interact With It Later?
```

For example:

```text
Can We Track It?

Can We Cancel It?

Can We Know When It Finishes?
```

The answer is:

```text
Yes
```

When launch creates a coroutine, it returns:

```text
Job
```

Conceptually:

```text
launch
↓
Create Coroutine
↓
Return Job
```

The Job acts as a handle to the coroutine.

We will study Job in detail later.

For now, think of it as:

```text
A Way To Interact With The Coroutine After It Has Started
```

---

## Mental Model For Job

Imagine ordering food.

You receive:

```text
Order Token
```

The token is not:

```text
The Food
```

Instead it allows you to:

```text
Track The Order

Check Its Status

Cancel It
```

Similarly:

```text
Job
```

is not the coroutine itself.

It is a handle that allows you to interact with the coroutine after it has been started.

Examples:

```text
Track Completion

Cancel Coroutine

Observe Lifecycle
```

We will study these capabilities in detail later.

---

## Mental Model For Job

Imagine ordering food.

You receive:

```text
Order Token
```

The token is not:

```text
The Food
```

Instead it helps you:

```text
Track The Order
```

Similarly:

```text
Job
```

is not the coroutine itself.

It is a handle that allows you to track and control the coroutine.

---

## launch And Results

Another important observation:

```text
launch
```

is generally used when you are interested in:

```text
Performing Work
```

rather than:

```text
Returning A Result
```

Conceptually:

```text
Need Work Done
↓
launch
```

Examples:

```text
Save Analytics

Update Database

Log Event

Refresh Cache
```

---

## Important Clarification

A common misconception:

```text
launch
↓
Returns Result
```

Incorrect.

launch returns:

```text
Job
```

not the result produced by the coroutine.

---

## Another Important Clarification

launch does NOT mean:

```text
Run On Background Thread
```

The builder:

```text
launch
```

creates a coroutine.

Thread selection is a separate topic that we will study later through:

```text
Dispatchers
```

---

## Another Important Clarification

launch does NOT mean:

```text
Run In Parallel
```

The builder simply creates a coroutine.

Questions about:

```text
Concurrency

Parallelism

Thread Usage
```

belong to later chapters.

---

## Why Is launch Important?

launch is the most common way to:

```text
Create

Start

Manage
```

coroutines that primarily perform work.

You will see it throughout Android applications.

---

## Looking Back

Notice how our mental model is evolving:

```text
Coroutine
↓
Suspendable Work

Continuation
↓
Remember How To Continue

Coroutine Builder
↓
Create Coroutine

launch
↓
Create And Start Coroutine
↓
Return Job
```

---

## The Next Question

At this point a natural question appears:

```text
Okay...

What If I Need A Result Back?
```

For example:

```text
Download User
↓
Need User Object

Calculate Value
↓
Need Computed Result
```

This question leads us to:

```text
async
```

which is another coroutine builder.

---

## Common Misconceptions

### Misconception 1

```text
launch = Coroutine
```

Incorrect.

launch is a coroutine builder.

---

### Misconception 2

```text
launch Returns Result
```

Incorrect.

launch returns a Job.

---

### Misconception 3

```text
launch Runs On Background Thread
```

Incorrect.

Thread selection is a separate concern.

---

### Misconception 4

```text
launch Automatically Means Parallelism
```

Incorrect.

launch creates a coroutine.

Parallelism depends on other factors.

---

## Relationship With Future Concepts

launch directly connects to:

```text
Job

async

CoroutineScope

Dispatchers
```

These concepts explain how launched coroutines are managed and executed.

---

> **You'll see this in...**
> - **DOC 5 — Android Lifecycle & Architecture**, where `viewModelScope.launch { ... }` is the most common way to start screen-related work — fetching data, processing user actions
> - **DOC 11 — Remote Communication**, where `launch` triggers network calls inside ViewModels
> - **DOC 15 — Background Work**, where `CoroutineWorker` uses `launch`-style coroutines for tasks that run even when the app is closed

---

## Revision Table

| Section | Content |
|----------|----------|
| Definition | Coroutine builder that creates and starts a coroutine |
| Returns | Job |
| Primary Purpose | Start work |
| Returns Result? | No |
| Creates Coroutine? | Yes |
| Abstraction Level | Coroutine Builder |
| Previous Concept | Coroutine Builder |
| Next Concept | async |
| Related Concepts | Job, Dispatchers |

---

## Future Exploration

- async
- Job
- CoroutineScope
- Dispatchers