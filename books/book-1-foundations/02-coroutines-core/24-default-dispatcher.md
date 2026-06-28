# Default Dispatcher

## The Problem

In the previous chapters we learned:

```text
Main Dispatcher
↓
UI Work
```

and:

```text
IO Dispatcher
↓
Waiting Work
```

A natural assumption appears:

```text
If Work Should Not Run On Main Thread

Use IO Dispatcher
```

However, this assumption is incomplete.

Not all non-UI work is the same.

---

## A New Kind Of Work

Consider:

```text
Sort 10 Million Items

Process An Image

Compress A File

Parse A Large JSON File

Run A Recommendation Algorithm
```

These operations are different from:

```text
Network Requests

Database Queries

File Reads
```

Why?

Because they are not primarily:

```text
Waiting
```

They are primarily:

```text
Computing
```

---

## Comparing Waiting Work And CPU Work

Consider a network request:

```text
Send Request
↓
Wait
↓
Wait
↓
Wait
↓
Receive Response
```

Most of the time:

```text
CPU
↓
Not Doing Much
```

The application is waiting for a response.

---

Now consider sorting a huge collection:

```text
Compare
Compare
Compare
Compare
Compare
```

During most of the operation:

```text
CPU
↓
Actively Working
```

This is a completely different workload.

---

## A New Problem Appears

If:

```text
IO Dispatcher
```

is intended for:

```text
Waiting Work
```

then a natural question appears:

```text
Where Should CPU-Intensive Work Run?
```

This is the problem that:

```text
Dispatchers.Default
```

solves.

---

## Enter Default Dispatcher

Conceptually:

```text
Dispatchers.Default
```

means:

```text
Run This Coroutine Using Threads Intended For CPU Work
```

Think of:

```text
Dispatchers.Default
```

as:

```text
The CPU Work Dispatcher
```

---

## First Mental Model

```text
Main Dispatcher
↓
UI Work
```

---

```text
IO Dispatcher
↓
Waiting Work
```

---

```text
Default Dispatcher
↓
CPU Work
```

This is the most important distinction in this chapter.

---

## Example

```kotlin
launch(Dispatchers.Default) {

    sortLargeCollection()
}
```

Conceptually:

```text
Coroutine
↓
Default Dispatcher
↓
Thread
↓
CPU Work
```

---

## Why Is This Important?

Imagine:

```kotlin
launch(Dispatchers.Main) {

    sortLargeCollection()
}
```

Conceptually:

```text
CPU Work
↓
Main Thread Busy
↓
UI Cannot Respond
```

This may lead to:

```text
UI Freeze

Jank

ANRs
```

---

Now compare:

```kotlin
launch(Dispatchers.Default) {

    sortLargeCollection()
}
```

Conceptually:

```text
CPU Work
↓
Default Dispatcher
↓
Background Thread
↓
Main Thread Remains Responsive
```

The UI can continue functioning normally.

---

## CPU Work vs Waiting Work

A common mistake is treating these as the same thing.

They are not.

---

Waiting Work:

```text
Send Request
↓
Wait
↓
Receive Response
```

The CPU spends much of its time idle.

---

CPU Work:

```text
Compute
Compute
Compute
Compute
```

The CPU spends much of its time busy.

---

This distinction is the reason both:

```text
IO Dispatcher
```

and:

```text
Default Dispatcher
```

exist.

---

## Revisiting Suspension

Consider:

```kotlin
launch(Dispatchers.Default) {

    delay(1000)

    processImage()
}
```

A natural question appears:

```text
What Happens When The Coroutine Suspends?
```

Just like every other dispatcher:

```text
Continuation Remembers What To Execute Next
```

and:

```text
Dispatcher Decides Where To Execute It
```

These responsibilities remain separate.

---

## Another Mental Model

Think of:

```text
Default Dispatcher
```

as:

```text
The Workspace For Computation
```

When the primary activity is:

```text
Using CPU
```

rather than:

```text
Waiting
```

Default Dispatcher is usually the correct choice.

---

## What Default Dispatcher Does NOT Mean

A common misconception is:

```text
Default Dispatcher = General Purpose Dispatcher For Everything
```

Incorrect.

It is specifically intended for:

```text
CPU-Intensive Work
```

---

## Another Common Misconception

A common misconception is:

```text
Default Dispatcher Makes Computation Faster
```

Not necessarily.

Its purpose is usually:

```text
Keep CPU Work Away From The Main Thread
```

so that the UI remains responsive.

---

## Another Common Misconception

A common misconception is:

```text
Network Requests Should Use Default Dispatcher
```

Incorrect.

Network requests are typically:

```text
Waiting Work
```

not:

```text
CPU Work
```

Those operations usually belong to:

```text
IO Dispatcher
```

---

## Another Common Misconception

A common misconception is:

```text
Default Dispatcher And IO Dispatcher Are Basically The Same
```

Incorrect.

Conceptually:

```text
IO Dispatcher
↓
Waiting Work
```

---

```text
Default Dispatcher
↓
CPU Work
```

They are designed for different workloads.

---

## A Natural Question

At this point, a natural question appears:

```text
Why Does Default Dispatcher Exist Separately From IO Dispatcher?
```

This is an excellent question.

The answer involves:

```text
CPU Cores

Thread Pools

Parallelism Limits

Dispatcher Internals
```

We will explore those topics in a future chapter.

For now, focus on:

```text
Waiting Work

And

CPU Work

Are Different Kinds Of Work
```

---

## The Relationship To Earlier Concepts

Recall:

```text
Main Thread
```

from Doc 1.

We learned:

```text
Heavy Work On Main Thread
↓
UI Freeze
↓
ANR
```

Default Dispatcher exists largely to move CPU-intensive work away from the main thread.

Conceptually:

```text
UI Work
↓
Main Dispatcher
```

---

```text
Waiting Work
↓
IO Dispatcher
```

---

```text
CPU Work
↓
Default Dispatcher
```

---

## The Key Mental Model

```text
Main Dispatcher
↓
UI Work
```

---

```text
IO Dispatcher
↓
Waiting Work
```

---

```text
Default Dispatcher
↓
CPU Work
```

Whenever a coroutine spends most of its time:

```text
Computing
```

rather than:

```text
Waiting
```

Default Dispatcher is usually the appropriate choice.

---

## Revision Table

| Section | Content |
|----------|----------|
| Type | Dispatcher |
| Main Purpose | Execute CPU-intensive work away from the main thread |
| Typical Work | Sorting, Parsing, Image Processing, Compression |
| Intended For | CPU Work |
| Intended For UI Updates? | No |
| Intended For Waiting Work? | No |
| Previous Concept | IO Dispatcher |
| Next Concept | Unconfined Dispatcher |

---

## Questions We Are Intentionally Postponing

You may naturally wonder:

```text
Why Does Default Dispatcher Use Fewer Threads Than IO Dispatcher?

How Many Threads Does It Have?

Why Doesn't Kotlin Create Hundreds Of Threads For CPU Work?

What Does CPU Core Count Have To Do With Dispatchers?
```

These are excellent questions.

They belong to a later chapter:

```text
Dispatcher Internals
```

For this chapter, focus on one idea:

```text
Dispatchers.Default
```

means:

```text
Run This Coroutine Using Threads Intended For CPU Work
```
