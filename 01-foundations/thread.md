# Thread

## What Problem Were We Facing?

In the previous concept, we learned that a process is a running program.

A process owns resources such as:

- Memory
- Variables
- Objects
- Files
- Network connections

However, a process itself is only a container.

This raises an important question:

```text
How is work organized inside a process?
```

Suppose Instagram is running.

The process may need to:

```text
Download Feed

Load Images

Handle User Input

Track Analytics
```

Some mechanism must exist to represent these independent flows of work.

The operating system solves this problem using **Threads**.

---

## Definition

A thread is an independently schedulable flow of execution inside a process.

In simpler terms:

```text
Process
↓
Container Of Resources

Thread
↓
Flow Of Work

CPU
↓
Executes That Work
```

A process owns resources.

A thread uses those resources to perform work.

---

## The Most Important Distinction

Many beginners confuse:

```text
Program
Process
Thread
CPU
```

Let's separate them clearly.

### Program

```text
Stored Instructions
```

Example:

```text
Instagram APK
```

Not running.

---

### Process

```text
Running Program
+
Resources
```

Example:

```text
Running Instagram
```

Owns:

```text
Memory

Files

Objects

Resources
```

---

### Thread

```text
Independent Flow Of Work
```

Examples:

```text
Handle UI

Download Feed

Load Images
```

A process may contain multiple threads.

---

### CPU

```text
Executes Instructions
```

The CPU performs the actual execution.

---

## Mental Model

Imagine a restaurant.

### Process

The restaurant.

```text
Kitchen

Tables

Ingredients

Equipment
```

These are resources.

---

### Threads

Orders being worked on.

```text
Order #1
↓
Prepare Pasta

Order #2
↓
Prepare Pizza
```

Each order represents an independent flow of work.

---

### CPU

The chef.

The chef performs the actual work.

---

Putting it together:

```text
Process
↓
Restaurant

Thread
↓
Order

CPU
↓
Chef
```

This mental model is surprisingly close to reality.

---

## Why Do Threads Exist?

Imagine Instagram only has one thread.

The thread starts:

```text
Download Feed
```

which takes 5 seconds.

Now the user taps:

```text
Like Button
```

Can the application respond immediately?

No.

Because the only thread is already busy.

The flow looks like:

```text
Download Feed
(5 seconds)
↓
Handle User Tap
```

The tap must wait.

---

Now imagine two threads.

```text
Thread A
↓
Download Feed

Thread B
↓
Handle User Input
```

Now user interactions do not need to wait for feed downloading.

This is why threads exist.

They allow independent flows of work.

---

## Every Process Has At Least One Thread

When a process is created:

```text
Operating System
↓
Creates Process
↓
Creates Initial Thread
```

Without a thread:

```text
No execution happens
```

because there would be no flow of work for the operating system to schedule.

---

## Multiple Threads Inside A Process

Example:

```text
Instagram Process
│
├── Thread A
│   └── Handle UI
│
├── Thread B
│   └── Download Feed
│
├── Thread C
│   └── Load Images
│
└── Thread D
    └── Analytics
```

All belong to the same process.

All share the same resources.

---

## Shared Resources

Threads inside the same process share:

```text
Memory

Objects

Files

Resources
```

Example:

```text
Instagram Process
│
├── Thread A
└── Thread B
```

Both can access the same user object.

This is powerful.

But it can also create problems.

Example:

```text
Thread A modifies value

Thread B modifies value
```

at the same time.

This can lead to:

```text
Race Conditions
```

We will revisit this later.

---

## What Makes One Thread Different From Another?

This is where many explanations become fuzzy.

Suppose we have:

```python
loadFeed()
loadImages()
showFeed()
```

Question:

```text
If two threads are running this code,
what makes them different?
```

The answer is:

```text
Execution State
```

Each thread maintains its own execution state.

---

## Program Counter

A thread keeps track of:

```text
Where execution currently is
```

Example:

```python
loadFeed()
loadImages()
showFeed()
```

Thread A might currently be:

```text
loadFeed()
^
```

Thread B might currently be:

```text
showFeed()
^
```

The information about the current position is maintained using a Program Counter (Instruction Pointer).

Think of it as:

```text
"Execute next instruction from here."
```

---

## Call Stack

A thread also maintains a call stack.

Example:

```python
main()
 └─ loadFeed()
     └─ downloadPosts()
```

The thread needs to know:

```text
How did I get here?

Where do I return after this function completes?
```

The call stack stores this information.

Each thread has its own call stack.

---

## Execution State

Program Counter and Call Stack are part of a larger concept:

```text
Execution State
```

Execution State contains everything required to continue execution later.

Conceptually:

```text
Execution State
│
├── Program Counter
├── Call Stack
├── Local Variables
└── Other CPU State
```

This becomes extremely important when we later discuss:

- Context Switching
- Coroutines
- Suspension
- Continuations

---

## One CPU Core, Multiple Threads

Suppose a phone has:

```text
One CPU Core
```

and

```text
Thread A
Thread B
```

Can both execute at the exact same instant?

No.

Only one instruction stream can run on the CPU core at a time.

However:

```text
Thread A
↓
Thread B
↓
Thread A
↓
Thread B
```

can be switched rapidly.

To humans, this appears simultaneous.

This concept is called:

```text
Concurrency
```

We will explore it in the next few chapters.

---

## Common Misconceptions

### Misconception 1

```text
Process = Thread
```

Incorrect.

A process can contain many threads.

---

### Misconception 2

```text
Thread = CPU
```

Incorrect.

CPU executes instructions.

Thread represents a schedulable flow of work.

---

### Misconception 3

```text
Thread = Program
```

Incorrect.

Program is the code.

Thread is a running flow through that code.

Multiple threads can execute the same program.

---

### Misconception 4

```text
Program Counter = Thread
```

Incorrect.

Program Counter is only one piece of a thread's execution state.

---

## Why Is This Concept Important?

Threads are the foundation of:

- Main Thread
- Background Thread
- Concurrency
- Parallelism
- Context Switching
- Coroutines
- Dispatchers

Understanding threads properly makes every future concept easier.

---

## Revision Table

| Section | Content |
|----------|----------|
| Definition | Independently schedulable flow of execution |
| Lives Inside | Process |
| Uses Resources From | Process |
| Actual Executor | CPU |
| Owns Program Counter | Yes |
| Owns Call Stack | Yes |
| Owns Execution State | Yes |
| Shares Process Memory | Usually Yes |
| Previous Concept | Process |
| Next Concept | Thread Scheduling |
| Related Concepts | Concurrency, Coroutines, Dispatchers |

---

## Future Exploration

- Thread Scheduling
- Context Switching
- Main Thread
- Background Thread
- Thread Safety
- Race Conditions
- Synchronization
- Thread Pools
