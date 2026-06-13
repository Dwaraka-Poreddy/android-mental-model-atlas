# CPU Cores

## A Question We Have Been Postponing

Throughout the dispatcher chapters, several questions naturally appeared:

```text
Why Does Default Dispatcher Care About CPU Count?

Why Doesn't Kotlin Create Hundreds Of Threads For CPU Work?

Why Doesn't More Threads Mean More Performance?
```

We intentionally postponed these questions because we were missing an important concept:

```text
CPU Core
```

This chapter answers those questions.

---

## A Reminder From Earlier Chapters

We previously learned:

```text
CPU
```

is responsible for:

```text
Executing Instructions
```

Conceptually:

```text
Program
↓
Instructions
↓
CPU Executes Them
```

However, a natural question appears:

```text
How Many Instructions Can A CPU Execute At The Same Time?
```

This question leads us to:

```text
CPU Cores
```

---

## Imagine A Single Worker

Suppose a factory has:

```text
1 Worker
```

and receives:

```text
10 Tasks
```

Conceptually:

```text
Worker
↓
Task 1

Worker
↓
Task 2

Worker
↓
Task 3
```

Only:

```text
One Task
```

can be actively worked on at a time.

The remaining tasks must wait.

---

## First Mental Model

Think of:

```text
CPU Core
```

as:

```text
A Worker That Executes Instructions
```

---

A CPU with:

```text
1 Core
```

is conceptually:

```text
1 Worker
```

---

A CPU with:

```text
4 Cores
```

is conceptually:

```text
4 Workers
```

---

A CPU with:

```text
8 Cores
```

is conceptually:

```text
8 Workers
```

---

## What Does A Core Actually Do?

A core is capable of:

```text
Executing Instructions
```

Conceptually:

```text
Instruction
↓
Core Executes
```

Every running program ultimately depends on CPU cores performing work.

---

## Why Do Multiple Cores Exist?

Consider:

```text
Task A

Task B

Task C

Task D
```

With:

```text
1 Core
```

conceptually:

```text
Task A
↓
Task B
↓
Task C
↓
Task D
```

The work must be shared.

---

With:

```text
4 Cores
```

conceptually:

```text
Core 1 → Task A

Core 2 → Task B

Core 3 → Task C

Core 4 → Task D
```

Multiple tasks can actively progress at the same time.

---

## Revisiting Parallelism

Back in Doc 1 we learned:

```text
Concurrency
```

and:

```text
Parallelism
```

A quick reminder:

---

Concurrency:

```text
Many Tasks Making Progress
```

---

Parallelism:

```text
Many Tasks Executing Simultaneously
```

---

CPU cores are one of the reasons true parallelism is possible.

---

## A Natural Question

Suppose a device has:

```text
8 CPU Cores
```

A natural question appears:

```text
Can More Than 8 CPU Tasks Run Simultaneously?
```

Conceptually:

```text
No
```

because there are only:

```text
8 Workers
```

available.

---

Additional tasks can still exist:

```text
Task 9

Task 10

Task 11
```

but they must wait for a core to become available.

---

## Why Is This Important?

Imagine:

```text
8 CPU Cores
```

and:

```text
100 CPU-Intensive Tasks
```

A common misconception is:

```text
100 Threads Means 100x Faster
```

Incorrect.

The CPU still only has:

```text
8 Workers
```

available.

---

The system must constantly switch between tasks.

We learned this concept earlier as:

```text
Context Switching
```

More threads do not automatically create more processing power.

---

## Why Doesn't Kotlin Create Hundreds Of Threads?

Now we can revisit one of our earlier questions.

Suppose:

```text
8 CPU Cores
```

exist.

A natural question appears:

```text
Why Not Create 500 CPU Threads?
```

Because:

```text
500 Threads
```

does not create:

```text
500 CPU Cores
```

The same limited number of cores must still execute the work.

---

This is one of the reasons thread count must be managed carefully.

---

## CPU Work vs Waiting Work

Consider:

```text
Image Processing

Sorting

Compression

Parsing
```

These are:

```text
CPU Work
```

The CPU spends most of its time actively working.

---

Now consider:

```text
Network Request

Database Query

File Read
```

These are usually:

```text
Waiting Work
```

The CPU spends much of its time waiting for an external resource.

---

This distinction becomes extremely important when we discuss dispatchers.

---

## Why Does Default Dispatcher Care About CPU Cores?

At this point, a natural question appears:

```text
If CPU Cores Limit Parallel CPU Work,

Should CPU Dispatchers Care About Core Count?
```

The answer is:

```text
Yes
```

This idea will become important in the next chapter.

---

## Another Common Misconception

A common misconception is:

```text
Thread = CPU Core
```

Incorrect.

A:

```text
Thread
```

is work that wants execution.

A:

```text
CPU Core
```

is a resource capable of executing that work.

Many threads may compete for a small number of CPU cores.

---

## Another Common Misconception

A common misconception is:

```text
More Threads Always Means More Speed
```

Incorrect.

Once CPU cores are fully utilized:

```text
Additional Threads May Add Overhead
```

rather than improving performance.

---

## The Relationship To Dispatchers

We recently learned:

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

The concept of CPU cores helps explain why:

```text
CPU Work
```

is treated differently from:

```text
Waiting Work
```

We will explore this relationship in the next chapter.

---

## The Key Mental Model

```text
CPU Core
```

is:

```text
A Worker That Executes Instructions
```

More CPU-intensive work requires:

```text
CPU Core Time
```

not merely:

```text
More Threads
```

This single idea explains why CPU resources are limited.

---

## Revision Table

| Section | Content |
|----------|----------|
| Type | Hardware Concept |
| Main Purpose | Execute instructions |
| Mental Model | Worker |
| More Cores Means | More simultaneous execution capacity |
| Same As Thread? | No |
| Enables Parallelism? | Yes |
| Previous Concept | Unconfined Dispatcher |
| Next Concept | Dispatcher Internals |

---

## Questions We Are Intentionally Postponing

You may naturally wonder:

```text
What Is A Thread Pool?

Why Does Default Dispatcher Use CPU Core Count?

Why Does IO Dispatcher Allow More Threads?

What Is Parallelism Limit?

How Does Kotlin Reuse Threads?
```

These are exactly the questions that the next chapter will answer.

For this chapter, focus on one idea:

```text
CPU Core
```

is:

```text
A Worker That Executes Instructions
```

and the number of workers limits how much CPU work can truly execute at the same time.
