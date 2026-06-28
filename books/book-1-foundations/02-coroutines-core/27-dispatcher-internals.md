# Dispatcher Internals

## A Question We Have Been Postponing

Throughout the dispatcher chapters, several questions naturally appeared:

```text
Why Does Default Dispatcher
Use Fewer Threads?

Why Does IO Dispatcher
Allow More Threads?

Why Not Create
Hundreds Of Threads
For Everything?

Why Doesn't More Threads
Mean More Performance?
```

This chapter answers those questions.

---

## A Common Assumption

A natural assumption is:

```text
More Threads
↓
More Performance
```

At first glance, this seems reasonable.

Suppose:

```text
8 Threads
```

perform work.

Why not create:

```text
80 Threads?

800 Threads?

8000 Threads?
```

Wouldn't that make the application faster?

A natural question appears:

```text
What Is The Limiting Factor?
```

---

## Revisiting CPU Cores

In the previous chapter we learned:

```text
CPU Core
↓
Worker
```

Suppose a device has:

```text
8 CPU Cores
```

Conceptually:

```text
8 Workers
```

exist.

A natural question appears:

```text
Can 500 CPU Tasks
Actively Execute
At The Same Time?
```

The answer is:

```text
No
```

because there are still only:

```text
8 Workers
```

available.

---

## Threads Want CPU Time

Remember:

```text
Thread
```

is:

```text
Work That Wants
Execution
```

and:

```text
CPU Core
```

is:

```text
The Resource
That Provides Execution
```

Many threads may exist.

However:

```text
Only A Limited Number
Can Use CPU
At The Same Time
```

because CPU cores are limited.

---

## The Real Problem

Suppose:

```text
8 CPU Cores
```

and:

```text
500 CPU Threads
```

exist.

Conceptually:

```text
500 Threads
Competing
For 8 Workers
```

The operating system must constantly decide:

```text
Who Runs Next?
```

This introduces:

```text
Scheduling Overhead
```

and:

```text
Context Switching
```

which we learned earlier in Doc 1.

---

## More Threads Is Not Infinite Performance

A common misconception is:

```text
More Threads
=
More Work Completed
```

Incorrect.

Once all CPU cores are busy:

```text
Additional Threads
Cannot Create
Additional CPU Power
```

The same limited CPU cores must still perform the work.

---

## A New Idea: Thread Pools

At this point, a natural question appears:

```text
If Creating Unlimited Threads
Doesn't Help,

Why Create Them At All?
```

This leads us to:

```text
Thread Pools
```

---

## What Is A Thread Pool?

Conceptually:

```text
Thread Pool
```

means:

```text
Reusable Threads
```

instead of:

```text
Create New Thread
For Every Task
```

Think of a thread pool as:

```text
A Group Of Workers
Waiting For Tasks
```

---

Conceptually:

```text
Task Arrives
↓
Available Thread Takes Task
↓
Task Completes
↓
Thread Returns To Pool
```

The thread is reused.

---

## Why Reuse Threads?

Creating threads is not free.

We learned earlier:

```text
Threads Have Cost
```

Creating and destroying threads repeatedly can waste resources.

Thread pools help by:

```text
Reusing Existing Threads
```

instead of constantly creating new ones.

---

## Revisiting Dispatchers

Recall:

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

A natural question appears:

```text
Should Waiting Work
And CPU Work
Use The Same Thread Pool?
```

The answer is:

```text
Not Necessarily
```

because they behave very differently.

---

## CPU Work Behaves Differently

Consider:

```text
Image Processing

Sorting

Compression

Parsing
```

These tasks are:

```text
CPU Work
```

The CPU remains busy for most of the task's lifetime.

Conceptually:

```text
Thread
↓
Using CPU
Almost Constantly
```

---

## Waiting Work Behaves Differently

Consider:

```text
Network Request

Database Query

File Read
```

These tasks are often:

```text
Waiting Work
```

Conceptually:

```text
Thread
↓
Waiting
↓
Waiting
↓
Waiting
```

The CPU is often not doing much during this time.

---

## Why Default Dispatcher Uses A Smaller Pool

Now we can revisit an earlier question.

Suppose:

```text
8 CPU Cores
```

exist.

A natural question appears:

```text
Should We Create
500 CPU Threads?
```

Usually:

```text
No
```

because:

```text
500 CPU Threads
Still Compete
For The Same 8 Cores
```

The extra threads add overhead without creating more CPU power.

---

This is why:

```text
Default Dispatcher
```

typically keeps its thread pool relatively small.

Conceptually:

```text
CPU Work
↓
Limited By CPU Cores
```

---

## Why IO Dispatcher Allows More Threads

Now consider:

```text
100 Network Requests
```

Many of them may spend most of their time:

```text
Waiting
```

rather than:

```text
Using CPU
```

A natural question appears:

```text
Can We Support
More Waiting Tasks
Than CPU Tasks?
```

The answer is:

```text
Yes
```

because waiting tasks are often not actively consuming CPU.

---

Conceptually:

```text
100 Tasks
↓
Many Waiting
↓
Few Actively Using CPU
```

This is one of the reasons:

```text
IO Dispatcher
```

can allow more threads than:

```text
Default Dispatcher
```

---

## Parallelism Limits

A natural question appears:

```text
Can Parallelism
Increase Forever?
```

The answer is:

```text
No
```

Eventually:

```text
CPU Cores
```

become the limiting factor.

Additional threads cannot create additional CPU resources.

---

## Another Mental Model

Think of:

```text
CPU Cores
```

as:

```text
Workers
```

and:

```text
Threads
```

as:

```text
People Requesting Work
```

You can increase:

```text
Requests
```

almost endlessly.

However:

```text
Workers
```

remain limited.

Eventually:

```text
Workers
```

become the bottleneck.

---

## What This Means For Dispatchers

We can now summarize the dispatcher design.

---

```text
Main Dispatcher
↓
Protect UI Work
```

---

```text
Default Dispatcher
↓
Manage CPU Work
```

---

```text
IO Dispatcher
↓
Manage Waiting Work
```

Each dispatcher exists because:

```text
Different Workloads
Have Different Characteristics
```

---

## The Key Mental Model

A common misconception is:

```text
More Threads
=
More Performance
```

The more accurate model is:

```text
CPU Cores
↓
Limit CPU Parallelism
```

and:

```text
Different Workloads
Require Different
Thread Pool Strategies
```

This single idea explains why:

```text
Default Dispatcher
```

and:

```text
IO Dispatcher
```

are designed differently.

---

## Revision Table

| Section | Content |
|----------|----------|
| Type | Internal Runtime Concept |
| Main Purpose | Explain dispatcher thread management |
| Introduces | Thread Pools |
| Explains | Thread Reuse |
| Explains | Parallelism Limits |
| Explains | Default Dispatcher Design |
| Explains | IO Dispatcher Design |
| Previous Concept | CPU Cores |
| Next Concept | Cancellation |

---

## A Natural Question

So far we have learned:

```text
How Coroutines Start

How Coroutines Suspend

How Coroutines Resume

How Coroutines Are Scheduled

How Dispatchers Use Threads
```

A natural question appears:

```text
Once A Coroutine Starts, Must It Always Run To Completion?
```

Or can something stop it before it finishes?

For example:

```text
User Leaves Screen

User Cancels Operation

Application No Longer Needs The Result
```

What happens to the coroutine in those situations?

This question leads us to:

```text
Cancellation
```

which we will explore next.

---

For this chapter, focus on one idea:

```text
Different Workloads Require Different Thread Pool Strategies
```

and that is why:

```text
Default Dispatcher
and
IO Dispatcher
```

are designed differently.
