# Thread Cost

## What Problem Were We Facing?

In the previous chapter, we learned that threads solve important problems.

They allow applications to:

```text
Keep UI Responsive

Perform Background Work

Handle Multiple Tasks
```

However, we also learned that developers eventually discovered a major issue:

```text
Threads Are Not Free
```

This raises an important question:

```text
What exactly makes a thread expensive?
```

Why can't we simply create:

```text
One Thread Per Task
```

and never worry about it again?

---

## Abstraction Level

In this chapter we are discussing operating system and application architecture concepts.

To keep the discussion practical, we will use phrases such as:

```text
Create Thread

Run Thread

Manage Thread
```

At a lower level:

```text
CPU
↓
Executes Instructions For Thread
```

However, this chapter focuses on the resources and overhead associated with threads.

---

## The Beginner's Solution

Imagine an application that needs to perform:

```text
Download Feed

Load Comments

Load Notifications

Load Profile

Load Messages
```

A beginner might think:

```text
Task A
↓
Create Thread

Task B
↓
Create Thread

Task C
↓
Create Thread

Task D
↓
Create Thread
```

This seems reasonable.

Each task gets its own thread.

No waiting.

No blocking.

Problem solved.

Or so it appears.

---

## The Hidden Cost

Creating a thread is much heavier than calling a function.

A thread is not just:

```text
A Few Lines Of Code
```

The operating system must allocate resources for it.

Examples include:

```text
Call Stack

Execution State

Scheduling Information

Memory
```

Every thread consumes resources.

---

## Cost 1: Memory

Each thread requires memory.

For example:

```text
Call Stack
```

must be allocated for the thread.

Remember from earlier chapters:

```text
Call Stack
↓
Stores Function Calls
```

Every thread needs its own stack.

Conceptually:

```text
Thread A
↓
Stack A

Thread B
↓
Stack B

Thread C
↓
Stack C
```

More threads means:

```text
More Memory Usage
```

---

## Cost 2: Execution State

Earlier we learned about:

```text
Program Counter

Call Stack

Execution State
```

The operating system must keep track of all of this for every thread.

Conceptually:

```text
Thread A
↓
Current State

Thread B
↓
Current State

Thread C
↓
Current State
```

The more threads that exist:

```text
More State To Manage
```

---

## Cost 3: Scheduling Overhead

The operating system decides:

```text
Which Thread Runs?

When Does It Run?

How Long Does It Run?
```

This process is called:

```text
Scheduling
```

Every additional thread increases the amount of scheduling work.

---

## Cost 4: Context Switching

We previously learned:

```text
Thread A Running
↓
Pause

Thread B Running
↓
Resume
```

The operating system must:

```text
Save State

Load State
```

This is called:

```text
Context Switching
```

Context switching is useful.

But it is not free.

More threads often lead to:

```text
More Context Switching
↓
More Overhead
```

---

## The Waiting Problem

Consider:

```text
Network Request
↓
Wait 5 Seconds
```

Most of those 5 seconds are spent waiting.

The CPU is often doing nothing for that task.

Yet:

```text
Entire Thread
↓
Occupied
```

The thread still exists.

Its memory still exists.

Its execution state still exists.

This is one of the biggest inefficiencies in traditional threading models.

---

## Real World Example

Imagine:

```text
1000 Network Requests
```

A naive design might create:

```text
1000 Threads
```

The result:

```text
Huge Memory Usage

Heavy Scheduling

Large Context Switching Cost
```

Even though many of those threads are simply waiting.

---

## The Industry Realization

Developers eventually realized:

```text
Most Tasks Spend Time Waiting
```

not:

```text
Actively Using CPU
```

Creating a dedicated thread for every task was often wasteful.

The industry began asking:

```text
Can We Reuse Threads?

Can We Reduce Thread Creation?

Can We Manage Threads Better?
```

This question led directly to:

```text
Thread Pools
```

---

## Important Clarification

A common misconception:

```text
Threads Are Slow
```

Incorrect.

Threads are incredibly useful.

The real issue is:

```text
Creating Large Numbers Of Threads
```

can become expensive.

---

## Another Important Clarification

A common misconception:

```text
Thread Cost = CPU Cost
```

Incorrect.

Thread cost includes:

```text
Memory

Scheduling

Context Switching

Execution State Management
```

not just CPU usage.

---

## Why Is This Important?

Understanding thread cost helps explain many later concepts.

Without understanding thread cost:

```text
Thread Pools

Executors

Dispatchers

Coroutines
```

can feel unnecessary.

With this understanding:

```text
Thread Reuse
```

starts making a lot of sense.

---

## The Next Question

If creating threads is expensive:

```text
Why Create New Threads For Every Task?
```

Wouldn't it be better to:

```text
Create Threads Once

Reuse Them Repeatedly
```

That idea led to:

```text
Thread Pools
```

which we will study next.

---

## Common Misconceptions

### Misconception 1

```text
Threads Are Free
```

Incorrect.

They require resources.

---

### Misconception 2

```text
One Thread Per Task Is Always Best
```

Incorrect.

Large numbers of threads create overhead.

---

### Misconception 3

```text
Thread Cost Only Means CPU Cost
```

Incorrect.

Many resources are involved.

---

### Misconception 4

```text
Threads Are Bad
```

Incorrect.

The problem is excessive thread creation.

---

## Relationship With Future Concepts

Thread Cost directly connects to:

```text
Thread Pools

Executors

Dispatchers

Coroutines
```

Understanding this chapter makes the motivation behind thread reuse much clearer.

---

## Revision Table

| Section | Content |
|----------|----------|
| Core Idea | Threads consume resources |
| Major Costs | Memory, Scheduling, Context Switching, State |
| Biggest Inefficiency | Waiting While Occupying A Thread |
| Are Threads Bad? | No |
| Abstraction Level | OS & Application Architecture |
| Previous Concept | Traditional Thread Problems |
| Next Concept | Thread Pools |
| Related Concepts | Executors, Dispatchers, Coroutines |

---

## Future Exploration

- Thread Pools
- Executors
- Dispatchers
- Coroutines
