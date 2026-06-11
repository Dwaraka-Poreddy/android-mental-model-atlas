# Context Switching

## What Problem Were We Facing?

In the previous chapter, we learned that the operating system scheduler rapidly switches between threads.

Example:

```text
Thread A
↓
Thread B
↓
Thread C
↓
Thread A
```

This allows multiple threads to make progress even when there is only one CPU core.

However, this raises an important question:

```text
When a thread is paused,
how does it later continue
from exactly where it stopped?
```

For example:

```python
loadFeed()
loadImages()
showFeed()
```

Suppose execution is currently here:

```text
loadImages()
^
```

The scheduler pauses the thread and runs another thread.

When the thread resumes later:

```text
How does it know to continue
from loadImages()
instead of starting over?
```

The answer is **Context Switching**.

---

## Definition

Context Switching is the process of:

```text
Saving the execution state
of one thread
↓
Restoring the execution state
of another thread
```

This allows the CPU to switch between threads while preserving their progress.

---

## Mental Model

Imagine a teacher helping students solve problems.

The teacher is currently helping:

```text
Student A
```

Halfway through the problem, the teacher decides:

```text
Pause Student A
↓
Help Student B
```

Before leaving, the teacher writes down:

```text
Current Step

Current Progress

Important Notes
```

Later, when returning to Student A:

```text
Read Notes
↓
Continue From Same Point
```

The notes are analogous to the thread's execution state.

The act of saving and restoring those notes is context switching.

---

## What Is Context?

The word "Context" means:

```text
Everything needed
to continue execution later.
```

A thread's context typically includes:

```text
Program Counter

Call Stack

Local Variables

CPU Register State
```

Without this information, the thread could not resume correctly.

---

## Program Counter

The Program Counter tracks:

```text
Which instruction should execute next.
```

Example:

```python
loadFeed()
loadImages()
showFeed()
```

Current position:

```text
loadImages()
^
```

The Program Counter stores information similar to:

```text
Execute next instruction from here.
```

When the thread resumes, execution continues from the correct location.

---

## Call Stack

The Call Stack tracks:

```text
How execution reached
the current point.
```

Example:

```python
main()
 └── loadFeed()
      └── downloadPosts()
```

Current execution:

```text
downloadPosts()
```

The thread must remember:

```text
Who called me?

Where do I return afterwards?
```

The Call Stack stores this information.

---

## Local Variables

Consider:

```python
fun downloadPosts() {
    val page = 3
}
```

If the thread is paused:

```text
page = 3
```

must not disappear.

Otherwise execution would resume incorrectly.

Local variable values are part of the thread's execution state.

---

## CPU Registers

The CPU also maintains internal working values.

When switching threads:

```text
Thread A Values
↓
Saved

Thread B Values
↓
Loaded
```

This allows each thread to continue as if it had never been interrupted.

You do not need to know register details yet.

Just remember:

```text
Registers are part of execution state.
```

---

## How Context Switching Works

Suppose we have:

```text
Thread A

Thread B
```

Current execution:

```text
CPU
↓
Running Thread A
```

Scheduler decides:

```text
Run Thread B
```

The operating system performs:

### Step 1

Save Thread A Context

```text
Program Counter

Call Stack

Variables

CPU State
```

---

### Step 2

Load Thread B Context

```text
Program Counter

Call Stack

Variables

CPU State
```

---

### Step 3

Resume Thread B

Execution continues from where Thread B previously stopped.

---

## Visual Flow

```text
CPU Running Thread A
          │
          ▼
Save Thread A Context
          │
          ▼
Load Thread B Context
          │
          ▼
CPU Running Thread B
```

This entire process happens extremely quickly.

---

## Why Is Context Switching Important?

Without context switching:

```text
Only One Thread
Could Make Progress
```

The operating system would not be able to share CPU time efficiently.

Context switching is what makes:

```text
Multiple Threads
```

practical.

---

## Android Example

Imagine:

```text
UI Thread
↓
Handle Touch Event

Network Thread
↓
Download Feed
```

The scheduler may rapidly switch between them:

```text
UI Thread
↓
Network Thread
↓
UI Thread
↓
Network Thread
```

Each switch requires saving and restoring context.

This allows:

```text
Responsive UI
+
Background Work
```

to coexist.

---

## Context Switching Has A Cost

A common misconception is:

```text
Context Switching Is Free
```

It is not.

Every switch requires:

```text
Save Context

Load Context

Scheduler Work
```

This consumes CPU time.

Too many context switches can reduce performance.

---

## Why Is This Important For Coroutines?

This chapter is one of the foundations for understanding coroutines.

Later we will learn:

```text
Threads
↓
Expensive Context Switching

Coroutines
↓
Cheaper Suspension & Resumption
```

Many coroutine benefits become easier to understand after learning context switching.

---

## Common Misconceptions

### Misconception 1

```text
Paused Thread Starts Over
```

Incorrect.

Its execution state is preserved.

---

### Misconception 2

```text
Program Counter = Thread
```

Incorrect.

Program Counter is only one part of a thread's context.

---

### Misconception 3

```text
Context Switching Is Free
```

Incorrect.

It has CPU overhead.

---

## Relationship With Future Concepts

Context Switching directly connects to:

```text
Concurrency

Parallelism

Main Thread

Coroutines

Dispatchers

Suspension

Continuation
```

Many future Android concepts become easier after understanding context switching.

---

## Revision Table

| Section | Content |
|----------|----------|
| Definition | Saving one thread's context and restoring another's |
| Purpose | Allow multiple threads to share CPU time |
| Context Includes | Program Counter, Call Stack, Variables, CPU State |
| Performed By | Operating System Scheduler |
| Free Operation? | No |
| Previous Concept | Thread Scheduling |
| Next Concept | Main Thread |
| Related Concepts | Concurrency, Coroutines, Continuations |

---

## Future Exploration

- Scheduler Internals
- CPU Registers
- Thread Priorities
- Coroutine Suspension
- Continuations
- Dispatchers
