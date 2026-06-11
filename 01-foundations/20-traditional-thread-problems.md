# Traditional Thread Problems

## What Problem Were We Facing?

So far, we have learned:

```text
Main Thread

Background Threads

Message Queue

Looper

Handler
```

Together, these concepts allow Android applications to perform work without freezing the UI.

For example:

```text
Main Thread
↓
Handle UI

Background Thread
↓
Download Data

Handler
↓
Request UI Update
```

This works.

In fact, Android applications were built this way for many years.

This raises an important question:

```text
If Threads, Handlers, Loopers and Queues already existed,

why did developers feel the need for something like Coroutines?
```

The answer is:

```text
These tools solve important problems.

But as applications grow, they create new problems.
```

---

## Abstraction Level

In this chapter we are discussing application architecture and developer experience.

We will use phrases such as:

```text
Thread Performs Work

Thread Communicates With Thread
```

At a lower level:

```text
CPU
↓
Executes Instructions
```

However, the focus of this chapter is:

```text
How Developers Organize Work
```

rather than CPU execution details.

---

## The Original Dream

The original idea seemed simple:

```text
Main Thread
↓
UI Work

Background Thread
↓
Long Running Work
```

Whenever work takes a long time:

```text
Network Request

Database Query

File Operation
```

simply move it to a background thread.

Problem solved.

Or so it seemed.

---

## The Reality

As applications grew larger, developers discovered that using threads effectively was much harder than expected.

The problems did not appear in:

```text
Small Apps
```

The problems appeared in:

```text
Real Apps
```

with:

```text
Multiple Screens

Multiple Features

Multiple Data Sources

Many Concurrent Operations
```

---

## Problem 1: Threads Are Not Free

Creating a thread requires resources.

Examples:

```text
Memory

Call Stack

Execution State

Scheduling Overhead
```

A few threads are usually fine.

However:

```text
10 Threads

100 Threads

1000 Threads
```

are very different situations.

As thread count increases:

```text
Resource Usage ↑

Complexity ↑
```

---

## Problem 2: Waiting Wastes Threads

Consider:

```text
Download User Data
```

The operation takes:

```text
5 Seconds
```

However:

```text
CPU Is Not Working for Most Of Those 5 Seconds
```

The thread is often simply waiting for:

```text
Network

Database

Disk
```

Yet an entire thread remains occupied.

This is wasteful.

---

## Problem 3: Communication Becomes Difficult

Suppose:

```text
Background Thread
↓
Download Data

Main Thread
↓
Update UI
```

Easy.

Now add:

```text
Feed

Comments

Notifications

Profile

Stories
```

Suddenly:

```text
Thread A
↓
Needs Main Thread

Thread B
↓
Needs Main Thread

Thread C
↓
Needs Main Thread
```

Communication becomes increasingly difficult to coordinate.

---

## Problem 4: More Moving Parts

To make thread communication work, developers often needed:

```text
Threads

Handlers

Loopers

Message Queues

Callbacks
```

A simple feature could involve multiple concepts.

Example:

```text
Background Thread
↓
Download Data

Handler
↓
Post Result

Message Queue
↓
Store Result

Looper
↓
Retrieve Result

Main Thread
↓
Update UI
```

Every step must be coordinated correctly.

---

## Problem 5: Shared State

Suppose:

```text
Thread A
↓
Update Counter

Thread B
↓
Update Counter
```

Questions immediately appear:

```text
Who Updates First?

What If Both Update Together?

Can Data Become Corrupted?
```

Managing shared state across threads is notoriously difficult.

---

## Problem 6: Debugging Becomes Hard

Suppose a bug occurs.

Questions arise:

```text
Which Thread Started The Work?

Which Thread Finished The Work?

Which Thread Updated The Data?

Which Thread Crashed?
```

As concurrency increases:

```text
Debugging Difficulty ↑
```

---

## Problem 7: Code Becomes Hard To Follow

Imagine this flow:

```text
Download Feed
↓
Update UI
↓
Download Comments
↓
Update UI
↓
Download Profile
↓
Update UI
```

The logical flow is simple.

However, thread coordination often spreads the code across multiple locations.

The developer starts thinking about:

```text
Which Thread?

Which Handler?

Which Queue?
```

instead of:

```text
What Is My Business Logic?
```

---

## The Industry Realization

Over time, developers realized:

```text
Threads Are Powerful
```

but also:

```text
Threads Are Heavy

Threads Are Difficult To Coordinate

Threads Are Easy To Misuse
```

The challenge was no longer:

```text
How Do I Run Work?
```

The challenge became:

```text
How Do I Manage Work?
```

---

## Important Clarification

A common misconception:

```text
Threads Are Bad
```

Incorrect.

Threads remain fundamental to modern software.

The real problem is:

```text
Managing Large Numbers Of Tasks using Threads Alone
```

becomes difficult.

---

## Why Is This Important?

Understanding these problems helps explain why developers began searching for:

```text
Simpler Async Programming

Cheaper Concurrency

Better Coordination Models
```

This search eventually led to:

```text
Executors

Thread Pools

Reactive Programming

Coroutines
```

---

## The Next Problem

Among all the challenges discussed so far, one deserves special attention:

```text
Threads Are Expensive
```

Understanding this problem is critical because it directly influenced the design of modern concurrency tools.

We will explore that next.

---

## Common Misconceptions

### Misconception 1

```text
Threads Solve All Concurrency Problems
```

Incorrect.

They introduce new challenges.

---

### Misconception 2

```text
More Threads = Better Performance
```

Incorrect.

Too many threads can hurt performance.

---

### Misconception 3

```text
Thread Communication Is Easy
```

Incorrect.

It becomes increasingly complex as applications grow.

---

### Misconception 4

```text
Coroutines Replace Threads
```

Incorrect.

Coroutines build on top of threads.

---

## Relationship With Future Concepts

Traditional Thread Problems directly connect to:

```text
Thread Cost

Thread Communication Complexity

Callback Hell

Coroutines

Dispatchers

Structured Concurrency
```

Understanding these problems makes the motivation behind coroutines much easier to understand.

---

## Revision Table

| Section | Content |
|----------|----------|
| Core Idea | Threads solve problems but create new ones |
| Major Issues | Cost, Waiting, Communication, Shared State, Debugging |
| Biggest Theme | Managing Work Becomes Difficult |
| Are Threads Bad? | No |
| Abstraction Level | Application Architecture |
| Previous Concept | Handler |
| Next Concept | Thread Cost |
| Related Concepts | Coroutines, Dispatchers, Async Programming |

---

## Future Exploration

- Thread Cost
- Thread Communication Complexity
- Callback Hell
- Executors
- Thread Pools
- Coroutines
