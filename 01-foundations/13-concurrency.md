# Concurrency

## What Problem Were We Facing?

In the previous chapter, we learned about non-blocking execution.

Instead of:

```text
Start Work
↓
Wait
↓
Continue
```

a thread can:

```text
Start Work
↓
Do Something Else
↓
Come Back Later
```

This creates an interesting opportunity.

Suppose Instagram needs to:

```text
Download Feed

Download Notifications
```

In a blocking world:

```text
Download Feed
↓
Wait
↓
Feed Arrives
↓
Download Notifications
↓
Wait
↓
Notifications Arrive
```

The thread spends much of its time waiting.

---

With non-blocking execution:

```text
Start Feed Download
↓
Don't Wait
```

The thread immediately becomes available.

This raises an important question:

```text
What should the thread do
while the feed download is still in progress?
```

One possibility is:

```text
Start Another Task
```

Example:

```text
Start Feed Download
↓
Start Notifications Download
```

Now:

```text
Feed Download
(In Progress)

Notifications Download
(In Progress)
```

Multiple tasks are making progress during the same period of time.

This idea leads us to:

```text
Concurrency
```

---

## Definition

Concurrency is the ability to make progress on multiple tasks during the same period of time.

The key phrase is:

```text
Make Progress
```

Concurrency does NOT require tasks to execute simultaneously.

Instead:

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

may all make progress over time.

---

## Mental Model

Imagine a chef preparing:

```text
Pasta

Pizza

Soup
```

The chef starts:

```text
Pasta
```

While the pasta boils:

```text
Start Soup
```

While the soup simmers:

```text
Prepare Pizza
```

Then later:

```text
Return To Pasta
```

The chef is making progress on multiple dishes.

However:

```text
Only One Dish Is Being Worked On
At Any Instant
```

This is concurrency.

---

## The Core Idea

Concurrency is about:

```text
Managing Multiple Tasks
```

It is NOT necessarily about:

```text
Running Multiple Tasks Simultaneously
```

This distinction is extremely important.

---

## Why Concurrency Exists

Let's revisit our earlier example.

Suppose:

```text
Feed Download
```

takes:

```text
5 Seconds
```

Most of those 5 seconds are spent waiting for the network.

If we block the thread:

```text
Thread
↓
Waits 5 Seconds
```

a lot of potential work is wasted.

---

Instead:

```text
Start Feed Download
↓
Do Something Else
```

Now the thread can:

```text
Handle Notifications

Prepare UI

Process Another Request
```

while the network operation continues.

Concurrency exists because waiting creates opportunities for other work to make progress.

---

## Visual Example

Without Concurrency:

```text
Task A
↓
Complete

Task B
↓
Complete

Task C
↓
Complete
```

One task must fully finish before the next begins.

---

With Concurrency:

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
↓
Task A
```

Multiple tasks progress together.

---

## Single CPU Core Example

Suppose we have:

```text
One CPU Core
```

and:

```text
Task A

Task B
```

The CPU cannot execute both simultaneously.

Instead:

```text
A
↓
B
↓
A
↓
B
↓
A
```

The operating system rapidly switches between them.

Both tasks continue making progress.

This is concurrency.

---

## Concurrency Does Not Require Multiple Threads

This surprises many developers.

Concurrency is NOT the same thing as:

```text
Multiple Threads
```

Example:

```text
Start Task A
↓
Pause

Start Task B
↓
Pause

Resume Task A
↓
Pause

Resume Task B
```

Even a single thread can manage multiple concurrent tasks.

This idea becomes extremely important when we study coroutines.

---

## Android Example

Suppose Instagram needs to:

```text
Download Feed

Download Notifications

Handle User Input
```

A concurrent system can make progress on all three activities during the same time period.

From the user's perspective:

```text
Feed Loads

Notifications Load

UI Remains Responsive
```

even though not everything is executing simultaneously.

---

## Concurrency Is About Structure

One useful way to think about it:

```text
Concurrency
↓
How Work Is Organized
```

It focuses on:

```text
Managing Multiple Tasks
```

rather than:

```text
How Many CPU Cores Exist
```

---

## Concurrency And Waiting

Concurrency becomes especially valuable when tasks spend time waiting.

Examples:

```text
Network Requests

Database Queries

File Access
```

Instead of:

```text
Wait
↓
Do Nothing
```

we can:

```text
Wait
↓
Do Other Work
```

This leads to better resource utilization.

---

## Important Clarification

Concurrency does NOT mean:

```text
Tasks Execute Simultaneously
```

A single CPU core can still support concurrency.

Remember:

```text
One Core
↓
One Instruction Stream At A Time
```

Yet concurrency remains possible.

---

## Concurrency And Coroutines

Coroutines are heavily focused on concurrency.

A coroutine may:

```text
Start Work
↓
Suspend
↓
Another Coroutine Runs
↓
Resume Later
```

Multiple tasks make progress without requiring large numbers of threads.

Understanding concurrency makes coroutine behavior much easier to understand.

---

## Common Misconceptions

### Misconception 1

```text
Concurrency = Parallelism
```

Incorrect.

They are related but different concepts.

---

### Misconception 2

```text
Concurrency Requires Multiple CPU Cores
```

Incorrect.

One core is enough.

---

### Misconception 3

```text
Concurrency Requires Multiple Threads
```

Incorrect.

One thread can manage multiple concurrent tasks.

---

### Misconception 4

```text
Concurrency Means Everything Happens At Once
```

Incorrect.

It means multiple tasks make progress during the same period.

---

## Relationship With Future Concepts

Concurrency directly connects to:

```text
Parallelism

Coroutines

Suspension

Dispatchers

Async Programming
```

Many modern Android APIs are designed around concurrent execution.

---

## Revision Table

| Section | Content |
|----------|----------|
| Definition | Multiple tasks making progress during the same period |
| Requires Simultaneous Execution? | No |
| Requires Multiple CPU Cores? | No |
| Requires Multiple Threads? | No |
| Focus | Managing Multiple Tasks |
| Why It Exists | Waiting creates opportunities for other work |
| Previous Concept | Non-Blocking |
| Next Concept | Parallelism |
| Related Concepts | Coroutines, Async Programming |

---

## Future Exploration

- Parallelism
- Structured Concurrency
- Coroutine Scheduling
- Dispatchers
- Async Programming
