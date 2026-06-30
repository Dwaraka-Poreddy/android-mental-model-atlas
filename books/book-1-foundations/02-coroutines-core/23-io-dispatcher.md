# IO Dispatcher

## The Problem

In the previous chapter we learned:

```text
Dispatchers.Main
```

which is used for:

```text
UI Work
```

Examples:

```text
Update Text

Show Loading Spinner

Display User Data

Navigate To Another Screen
```

However, a natural question appears:

```text
Should All Work Run On The Main Thread?
```

The answer is:

```text
No
```

---

## A Reminder From Earlier Chapters

Back in Doc 1 we learned:

```text
Heavy Work On Main Thread
↓
UI Freeze
↓
ANR
```

The main thread is responsible for:

```text
Drawing UI

Handling Touch Events

Running UI Logic
```

If we keep the main thread busy for too long:

```text
The UI Cannot Respond
```

---

## A New Problem Appears

Consider:

```kotlin
launch(Dispatchers.Main) {

    fetchUser()
}
```

Suppose:

```text
fetchUser()
```

takes:

```text
2 Seconds
```

A natural question appears:

```text
Should This Run On The Main Thread?
```

The answer is:

```text
No
```

because while the operation is running:

```text
UI Cannot Progress
```

---

## What Kind Of Work Are We Talking About?

Examples include:

```text
Network Requests

Database Queries

Reading Files

Writing Files
```

Notice something important.

Most of these operations are not spending their time:

```text
Doing Computation
```

Instead they are spending their time:

```text
Waiting
```

---

## Example: Network Request

Conceptually:

```text
Send Request
↓
Wait For Server
↓
Receive Response
```

Most of the time is spent:

```text
Waiting For Server
```

not:

```text
Using CPU
```

---

## Example: File Read

Conceptually:

```text
Request File
↓
Wait For Disk
↓
Receive Data
```

Again:

```text
Waiting
```

is the dominant activity.

---

## Example: Database Query

Conceptually:

```text
Send Query
↓
Wait For Database
↓
Receive Result
```

Once again:

```text
Waiting
```

is the primary activity.

---

## A Pattern Appears

These operations look different:

```text
Network

Database

Files
```

but they share one important characteristic:

```text
They Spend Most Of Their Time Waiting
```

This is the problem that:

```text
Dispatchers.IO
```

was created to solve.

---

## Enter IO Dispatcher

Conceptually:

```text
Dispatchers.IO
```

means:

```text
Run This Coroutine Using Threads Intended For Waiting Work
```

Think of:

```text
Dispatchers.IO
```

as:

```text
The Waiting Work Dispatcher
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

This is the most important distinction in this chapter.

---

## Example

```kotlin
launch(Dispatchers.IO) {

    fetchUser()
}
```

Conceptually:

```text
Coroutine
↓
IO Dispatcher
↓
Thread
↓
Network Request
```

The main thread remains free to continue handling UI.

---

## Why Is This Important?

Imagine:

```kotlin
launch(Dispatchers.Main) {

    fetchUser()
}
```

Conceptually:

```text
Network Request
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
launch(Dispatchers.IO) {

    fetchUser()
}
```

Conceptually:

```text
Network Request
↓
IO Dispatcher
↓
Background Thread
↓
Main Thread Remains Free
```

The UI can continue responding normally.

---

## Revisiting Suspension

Consider:

```kotlin
launch(Dispatchers.IO) {

    delay(1000)

    fetchUser()
}
```

A natural question appears:

```text
What Happens When The Coroutine Suspends?
```

The answer is:

```text
The Coroutine Suspends

The Dispatcher Remains Responsible For Future Execution
```

Just like we learned earlier:

```text
Continuation Remembers What To Execute Next
```

and:

```text
Dispatcher Decides Where To Execute It
```

These are separate responsibilities.

---

## Another Mental Model

Think of:

```text
Continuation
```

as:

```text
Saved Progress
```

and:

```text
IO Dispatcher
```

as:

```text
Thread Assignment For Waiting Work
```

Conceptually:

```text
Continuation
↓
What Runs Next

Dispatcher
↓
Where It Runs
```

---

## What IO Dispatcher Does NOT Mean

A common misconception is:

```text
IO Dispatcher = Network Dispatcher
```

Incorrect.

Network operations are only one example.

IO Dispatcher is generally intended for:

```text
Waiting Work
```

including:

```text
Network

Database

Files
```

---

## Another Common Misconception

A common misconception is:

```text
IO Dispatcher Makes Work Faster
```

Not necessarily.

The goal is usually:

```text
Keep Main Thread Free
```

rather than:

```text
Make Operations Faster
```

---

## Another Common Misconception

A common misconception is:

```text
Everything Should Run On IO Dispatcher
```

Incorrect.

Some work is not:

```text
Waiting Work
```

For example:

```text
Image Processing

Large Calculations

Sorting Huge Collections

Data Compression
```

These are primarily:

```text
CPU Work
```

and belong to a different dispatcher.

We will discuss that in the next chapter.

---

## A Natural Question

At this point, a natural question appears:

```text
If Most Network Requests Spend Their Time Waiting...

Why Do They Need Threads At All?
```

This is an excellent question.

Answering it properly requires concepts we have not yet learned:

```text
Thread Pools

Dispatcher Internals

Scheduler Design
```

We will explore those topics in a future chapter.

For now, focus on:

```text
Waiting Work Should Not Block The Main Thread
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
Blocking Main Thread
↓
UI Freeze
↓
ANR
```

IO Dispatcher exists largely to help move waiting work away from the main thread.

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

Whenever a coroutine performs work that spends most of its time:

```text
Waiting
```

rather than:

```text
Computing
```

IO Dispatcher is usually the appropriate choice.

---

## Revision Table

| Section | Content |
|----------|----------|
| Type | Dispatcher |
| Main Purpose | Execute waiting work without blocking the main thread |
| Typical Work | Network, Database, File Operations |
| Intended For | Waiting Work |
| Intended For UI Updates? | No |
| Intended For CPU-Heavy Work? | No |
| Previous Concept | Main Dispatcher |
| Next Concept | Default Dispatcher |

---

> **You'll see this in...**
> - **DOC 11 — Remote Communication**, where every Retrofit call and OkHttp request runs on `Dispatchers.IO`
> - **DOC 12 — Local Persistence**, where Room DAO queries switch to IO threads behind the scenes
> - **DOC 13 — Data Architecture**, where Repositories use `flowOn(Dispatchers.IO)` so data loading never blocks the UI

---

## Questions We Are Intentionally Postponing

You may naturally wonder:

```text
Why Does IO Dispatcher Allow More Threads?

How Many Threads Does It Have?

How Does It Differ Internally From Default Dispatcher?

How Does Kotlin Reuse Threads?
```

These are excellent questions.

They belong to a later chapter:

```text
Dispatcher Internals
```

For this chapter, focus on one idea:

```text
Dispatchers.IO
```

means:

```text
Run This Coroutine Using Threads Intended For Waiting Work
```
