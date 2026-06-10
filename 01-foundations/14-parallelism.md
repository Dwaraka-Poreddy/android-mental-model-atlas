# Parallelism

## What Problem Were We Facing?

In the previous chapter, we learned about concurrency.

Concurrency allows multiple tasks to make progress during the same period of time.

Example:

```text
Task A
↓
Task B
↓
Task A
↓
Task C
↓
Task B
```

Multiple tasks are progressing.

However, this raises an important question:

```text
Are multiple tasks actually running
at the exact same time?
```

The answer is:

```text
Not Always
```

Concurrency and Parallelism are related concepts, but they are not the same thing.

To understand the difference, we need to learn:

```text
Parallelism
```

---

## Definition

Parallelism is the ability to execute multiple tasks simultaneously.

The key word is:

```text
Simultaneously
```

Multiple tasks are literally running at the same instant.

---

## Mental Model

Imagine two chefs.

Chef A:

```text
Cook Pasta
```

Chef B:

```text
Cook Pizza
```

Both chefs work at the same time.

```text
Pasta Progressing

Pizza Progressing
```

simultaneously.

This is parallelism.

---

Compare this to concurrency.

One chef:

```text
Cook Pasta
↓
Cook Pizza
↓
Cook Pasta
↓
Cook Pizza
```

Multiple dishes make progress.

But only one dish is actively being worked on at any instant.

That is concurrency.

---

## The Core Difference

### Concurrency

```text
Multiple Tasks
↓
Making Progress
```

May or may not be simultaneous.

---

### Parallelism

```text
Multiple Tasks
↓
Executing Simultaneously
```

Must be simultaneous.

---

## Visual Example

### Concurrency

```text
CPU Core

Task A
↓
Task B
↓
Task A
↓
Task B
```

Tasks alternate.

---

### Parallelism

```text
Core 1
↓
Task A

Core 2
↓
Task B
```

Both tasks execute at the same time.

---

## Why Multiple CPU Cores Matter

Suppose we have:

```text
One CPU Core
```

The CPU can only execute:

```text
One Instruction Stream
```

at any instant.

Therefore:

```text
True Parallelism
```

is not possible on a single core.

The system may still support:

```text
Concurrency
```

through scheduling and context switching.

---

Now suppose we have:

```text
Core 1

Core 2
```

The operating system may schedule:

```text
Task A → Core 1

Task B → Core 2
```

Both tasks execute simultaneously.

This is parallelism.

---

## Concurrency Without Parallelism

This is extremely common.

Example:

```text
One CPU Core

Many Tasks
```

The scheduler rapidly switches between tasks.

```text
Task A
↓
Task B
↓
Task A
↓
Task C
```

Multiple tasks make progress.

But only one task executes at a time.

This is:

```text
Concurrency
Without
Parallelism
```

---

## Parallelism Usually Implies Concurrency

If:

```text
Task A

Task B
```

are running simultaneously,

then:

```text
Both Tasks Are Making Progress
```

which means concurrency also exists.

Think of it as:

```text
Parallelism
⊂
Concurrency
```

In practice:

```text
Parallelism
Usually Includes Concurrency
```

but not vice versa.

---

## Android Example

Suppose Instagram needs:

```text
Download Feed

Decode Images

Handle Notifications
```

---

On a single-core device:

```text
Scheduler
↓
Switches Between Tasks
```

This is concurrency.

---

On a multi-core device:

```text
Core 1
↓
Decode Images

Core 2
↓
Download Feed

Core 3
↓
Handle Notifications
```

Tasks execute simultaneously.

This is parallelism.

---

## Why Parallelism Exists

Parallelism helps improve:

```text
Throughput

Performance

Task Completion Time
```

Because more work can happen at once.

---

Example:

Without parallelism:

```text
Task A
↓
10 Seconds

Task B
↓
10 Seconds

Total
↓
20 Seconds
```

---

With parallelism:

```text
Task A
↓
10 Seconds

Task B
↓
10 Seconds

Run Together

Total
↓
10 Seconds
```

Work completes sooner.

---

## Parallelism And Coroutines

A common misconception:

```text
Coroutines Create Parallelism
```

Not necessarily.

Coroutines primarily help with:

```text
Concurrency
```

Parallelism depends on:

```text
Threads

Dispatchers

CPU Resources
```

We will revisit this when studying Dispatchers.

---

## Common Misconceptions

### Misconception 1

```text
Concurrency = Parallelism
```

Incorrect.

They are different concepts.

---

### Misconception 2

```text
Multiple Tasks
=
Parallelism
```

Incorrect.

Tasks may simply be concurrent.

---

### Misconception 3

```text
Parallelism Requires Coroutines
```

Incorrect.

Parallelism existed long before coroutines.

---

### Misconception 4

```text
One CPU Core Can Execute
Multiple Tasks Simultaneously
```

Incorrect.

One core executes one instruction stream at a time.

---

## Relationship With Future Concepts

Parallelism directly connects to:

```text
UI Freeze

ANR

Thread Pools

Dispatchers

Coroutines
```

Understanding the distinction between concurrency and parallelism prevents many future misconceptions.

---

## Revision Table

| Section | Content |
|----------|----------|
| Definition | Multiple tasks executing simultaneously |
| Requires Simultaneous Execution? | Yes |
| Requires Multiple CPU Cores? | Usually Yes |
| Focus | Executing Multiple Tasks At Once |
| Concurrency Required? | Yes |
| Previous Concept | Concurrency |
| Next Concept | UI Freeze |
| Related Concepts | Coroutines, Dispatchers, Thread Pools |

---

## Future Exploration

- Thread Pools
- CPU Core Scheduling
- Dispatchers.Default
- Parallel Computation
- Work Stealing
