# Thread Scheduling

## What Problem Were We Facing?

In the previous chapter, we learned that a process can contain multiple threads.

Example:

```text
Instagram Process
│
├── Thread A
│   └── Download Feed
│
├── Thread B
│   └── Handle User Input
│
└── Thread C
    └── Load Images
```

This raises an important question:

```text
How can multiple threads make progress
when there is only one CPU core?
```

A CPU core can only execute one instruction stream at a time.

So how can multiple threads appear to run simultaneously?

The answer is **Thread Scheduling**.

---

## Definition

Thread Scheduling is the process by which the operating system decides:

- Which thread runs next
- How long it runs
- When it should stop
- Which thread should run after it

In simple terms:

```text
Many Threads Want CPU Time
↓
Operating System Decides
↓
Who Gets CPU Time Next
```

---

## Mental Model

Imagine a teacher helping students during an exam.

There is:

```text
One Teacher

Many Students
```

All students need attention.

However, the teacher can only help one student at a time.

The teacher may do:

```text
Student A
↓
Student B
↓
Student C
↓
Student A
↓
Student B
```

very quickly.

To the students, it feels like the teacher is helping everyone.

But in reality:

```text
One Student At A Time
```

The teacher is acting like the scheduler.

---

## The Core Problem

Suppose we have:

```text
One CPU Core
```

and

```text
Thread A
Thread B
Thread C
```

The CPU cannot execute:

```text
A + B + C
```

simultaneously.

It must execute:

```text
A
↓
B
↓
C
```

one at a time.

The scheduler determines this order.

---

## Time Slicing

The operating system usually gives each thread a small amount of CPU time.

Example:

```text
Thread A
↓ 5 ms

Thread B
↓ 5 ms

Thread C
↓ 5 ms

Thread A
↓ 5 ms

Thread B
↓ 5 ms
```

This happens extremely quickly.

Humans perceive:

```text
Everything Happening Together
```

even though only one thread is executing at any given instant.

---

## Why Does This Feel Simultaneous?

Suppose the scheduler switches threads thousands of times per second.

Example:

```text
A
↓
B
↓
C
↓
A
↓
B
↓
C
```

The switching is so fast that we perceive:

```text
Parallel Activity
```

even though execution is actually alternating.

This is one of the foundations of:

```text
Concurrency
```

which we will explore later.

---

## What Information Does The Scheduler Need?

Suppose Thread A is currently executing.

```python
downloadFeed()
```

The scheduler decides:

```text
Pause Thread A
Run Thread B
```

Question:

```text
How does Thread A continue later
from exactly where it stopped?
```

The answer is:

```text
Execution State
```

Each thread has its own execution state.

Examples:

- Program Counter
- Call Stack
- Local Variables

The operating system saves this state before pausing the thread.

Later it restores the state and execution continues.

---

## Example

Suppose Thread A is executing:

```python
loadFeed()
loadImages()
showFeed()
```

Current state:

```text
Executing loadImages()
```

Scheduler decides:

```text
Pause Thread A
Run Thread B
```

Later:

```text
Resume Thread A
```

Thread A continues:

```text
loadImages()
```

instead of starting from the beginning.

This is possible because its execution state was preserved.

---

## Android Example

Suppose Instagram has:

```text
Thread A
↓
Download Feed

Thread B
↓
Handle User Input
```

The scheduler may do:

```text
Run A
↓
Run B
↓
Run A
↓
Run B
↓
Run A
```

very rapidly.

As a result:

```text
Feed Downloads
+
UI Remains Responsive
```

at the same time.

To the user:

```text
Everything Feels Simultaneous
```

---

## Scheduling Is Not Fairness

A common misconception is:

```text
Every thread gets equal CPU time.
```

Not necessarily.

The operating system may prioritize some threads over others.

Examples:

```text
UI Thread
↓
High Priority

Background Analytics
↓
Lower Priority
```

This helps maintain responsiveness.

---

## Single Core vs Multi-Core

### Single Core

```text
One Thread Executes At A Time
```

The scheduler rapidly switches between threads.

---

### Multi-Core

Example:

```text
Core 1
↓
Thread A

Core 2
↓
Thread B
```

Now multiple threads can truly execute simultaneously.

This is called:

```text
Parallelism
```

We will revisit this later.

---

## Why Is Thread Scheduling Important?

Thread Scheduling is the foundation for:

- Context Switching
- Concurrency
- Parallelism
- Main Thread
- Coroutines

Without scheduling:

```text
Multiple Threads
```

would not be practical.

---

## Common Misconceptions

### Misconception 1

```text
Multiple Threads
=
Always Simultaneous
```

Incorrect.

On a single CPU core, only one thread executes at a time.

---

### Misconception 2

```text
Multiple Threads
=
Multiple CPU Cores
```

Incorrect.

A single core can still manage many threads through scheduling.

---

### Misconception 3

```text
Paused Thread Loses Progress
```

Incorrect.

The thread's execution state is preserved.

---

## Relationship With Future Concepts

Thread Scheduling directly leads to:

```text
Context Switching

Concurrency

Parallelism

Main Thread

Coroutines
```

Understanding scheduling makes all of these concepts easier.

---

## Revision Table

| Section | Content |
|----------|----------|
| Definition | OS decides which thread gets CPU time |
| Purpose | Share CPU among multiple threads |
| Key Technique | Time Slicing |
| Requires | Execution State |
| Single Core | One thread executes at a time |
| Multi-Core | Multiple threads may execute simultaneously |
| Previous Concept | Thread |
| Next Concept | Context Switching |
| Related Concepts | Concurrency, Parallelism |

---

## Future Exploration

- Context Switching
- Scheduling Algorithms
- Thread Priorities
- Concurrency
- Parallelism
- Coroutines
