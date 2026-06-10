# Blocking

## What Problem Were We Facing?

In the previous chapters, we learned:

```text
Main Thread
↓
UI Work

Background Thread
↓
Long Running Work
```

We also learned an important rule:

```text
Do Not Perform Long Running Work
On The Main Thread
```

This naturally raises a question:

```text
Why?
```

Why is a network request dangerous on the Main Thread?

Why can a database query freeze the UI?

Why do Android developers constantly talk about:

```text
Blocking The Main Thread
```

To answer these questions, we first need to understand what **Blocking** means.

---

## Definition

Blocking occurs when a thread cannot make progress because it is waiting for some operation to complete.

In simple terms:

```text
Thread
↓
Starts Work
↓
Must Wait
↓
Cannot Continue
```

While waiting:

```text
The Thread Is Blocked
```

---

## Mental Model

Imagine a restaurant worker.

The worker receives a task:

```text
Bring Ingredients
```

The worker walks to the storage room.

Unfortunately:

```text
Storage Room Locked
```

Now the worker must wait.

While waiting:

```text
Cannot Cook

Cannot Take Orders

Cannot Help Customers
```

The worker is blocked.

The same idea applies to threads.

---

## Example

Suppose a thread executes:

```text
Download Feed Data
```

The data lives on a remote server.

The thread sends a request and then waits.

```text
Send Request
↓
Wait For Server
↓
Receive Response
```

During the waiting period:

```text
Thread Cannot Continue
```

The thread is blocked.

---

## Blocking Is About Waiting

Many beginners think:

```text
Blocking
=
Doing Heavy Work
```

Not necessarily.

Blocking specifically means:

```text
Waiting
```

Examples:

```text
Waiting For Network

Waiting For Database

Waiting For File

Waiting For Another Thread

Waiting For User Input
```

The thread is unable to continue until something else happens.

---

## Visual Example

Normal Execution:

```text
Task A
↓
Task B
↓
Task C
↓
Task D
```

The thread continuously makes progress.

---

Blocked Execution:

```text
Task A
↓
Wait...
↓
Wait...
↓
Wait...
↓
Task B
```

Progress temporarily stops.

---

## Why Is Blocking A Problem?

While blocked:

```text
Thread Exists

Memory Exists

Resources Exist
```

but:

```text
No Useful Work Happens
```

The thread is simply waiting.

---

Imagine:

```text
1000 Threads
```

all waiting for network responses.

The operating system must still:

```text
Track Them

Schedule Them

Store Their Context
```

even though they are mostly idle.

This becomes expensive.

We will revisit this later when discussing thread cost.

---

## Blocking On A Background Thread

Suppose:

```text
Background Thread
↓
Download Feed
```

The thread waits 5 seconds.

During those 5 seconds:

```text
Background Thread Blocked
```

This is usually acceptable.

Why?

Because:

```text
UI Remains Responsive
```

The user can still interact with the application.

---

## Blocking On The Main Thread

Now suppose:

```text
Main Thread
↓
Download Feed
```

The thread waits 5 seconds.

During those 5 seconds:

```text
No Touch Handling

No Scrolling

No Drawing

No UI Updates
```

The application appears frozen.

This is why Android developers are so concerned about blocking the Main Thread.

---

## Android Example

User opens Instagram.

The feed requires:

```text
Network Request
```

Bad approach:

```text
Main Thread
↓
Wait For Network
↓
Show Feed
```

Good approach:

```text
Background Thread
↓
Wait For Network

Main Thread
↓
Remain Responsive
```

The waiting still happens.

But it no longer freezes the UI.

---

## Blocking Is Not A Bug

Another common misconception:

```text
Blocking
=
Wrong
```

Not necessarily.

Blocking is a normal part of computing.

Example:

```text
Read File

Wait For Network

Query Database
```

All of these naturally involve waiting.

The real question is:

```text
Which Thread Is Being Blocked?
```

Blocking the Main Thread is usually problematic.

Blocking a Background Thread may be acceptable depending on the situation.

---

## Why Is This Important For Coroutines?

This chapter is one of the foundations for understanding coroutines.

Traditional threading often works like this:

```text
Need To Wait
↓
Block Thread
↓
Resume Later
```

Coroutines introduce a different approach:

```text
Need To Wait
↓
Suspend Work
↓
Free Thread
↓
Resume Later
```

That distinction becomes one of the biggest motivations for coroutines.

---

## Common Misconceptions

### Misconception 1

```text
Blocking = Heavy Computation
```

Incorrect.

Blocking means waiting.

---

### Misconception 2

```text
Blocking = Crash
```

Incorrect.

The application may still be running.

The thread is simply waiting.

---

### Misconception 3

```text
All Blocking Is Bad
```

Incorrect.

Blocking is often necessary.

The important question is:

```text
Which Thread Is Being Blocked?
```

---

### Misconception 4

```text
Background Threads Cannot Be Blocked
```

Incorrect.

Any thread can be blocked.

---

## Relationship With Future Concepts

Blocking directly connects to:

```text
Non-Blocking

Concurrency

UI Freeze

ANR

Coroutines

Suspension
```

Understanding blocking is essential before learning why coroutines became popular.

---

## Revision Table

| Section | Content |
|----------|----------|
| Definition | Thread waits and cannot make progress |
| Core Idea | Waiting |
| Can Happen On | Any Thread |
| Main Thread Blocking | Usually Bad |
| Background Thread Blocking | Often Acceptable |
| Previous Concept | Background Thread |
| Next Concept | Non-Blocking |
| Related Concepts | Coroutines, Suspension, ANR |

---

## Future Exploration

- Non-Blocking Programming
- Suspension
- Async I/O
- Coroutines
- Thread Pools
- ANR
