# CPU

## What Problem Were We Facing?

In the previous concept, we learned that a program is a collection of instructions.

However, a program by itself cannot do anything.

This raises an important question:

```text
Who actually executes these instructions?
```

Who performs calculations?

Who follows the instructions written inside a program?

Who makes a computer "do work"?

The answer is the **CPU**.

---

## Definition

CPU stands for **Central Processing Unit**.

The CPU is the component responsible for executing instructions and performing computations.

Whenever a program runs, its instructions are ultimately executed by the CPU.

In simple terms:

```text
Program
↓
Contains Instructions
↓
CPU Executes Them
```

---

## Mental Model

Think of a recipe book.

The recipe contains instructions:

```text
1. Cut vegetables
2. Heat oil
3. Cook vegetables
```

But the recipe cannot execute itself.

A chef must perform those actions.

Similarly:

```text
Program
↓
Instructions
```

requires:

```text
CPU
↓
Executes Instructions
```

The CPU is like the chef that follows the recipe.

---

## What Does The CPU Actually Do?

At a high level, the CPU repeatedly performs a simple cycle:

```text
Fetch Instruction
↓
Understand Instruction
↓
Execute Instruction
↓
Move To Next Instruction
```

Examples of instructions:

```text
Add two numbers

Compare two values

Store data

Load data

Jump to another instruction
```

Modern software is built from billions of these tiny operations.

---

## Why Is The CPU Important?

Every operation performed by a computer eventually reaches the CPU.

Examples:

```text
Opening Instagram
Playing a Video
Sending a Message
Scrolling a Feed
Calculating 2 + 2
```

All of these require CPU work.

No CPU means:

```text
No execution
No computation
No running applications
```

---

## CPU Is A Limited Resource

A computer does not have unlimited CPU power.

At any given moment:

```text
Many Programs
↓
Compete For CPU Time
```

Examples:

```text
Chrome
Spotify
VS Code
Slack
Android Studio
```

may all be running simultaneously.

The operating system must decide:

```text
Who gets CPU time?
For how long?
In what order?
```

We will revisit this idea when discussing threads and scheduling.

---

## Android Example

Suppose you tap the Instagram icon.

Android performs many operations:

```text
Start Process

Load Data

Initialize UI

Render Screen
```

All of these tasks eventually require CPU execution.

The CPU is doing the actual work behind the scenes.

---

## Common Misconception

Many beginners think:

```text
Program = Work
```

But programs do not perform work by themselves.

Programs only describe what should happen.

The CPU is what actually performs the work.

Think of it as:

```text
Program
↓
Instructions

CPU
↓
Execution
```

---

## Relationship With Future Concepts

The CPU is closely related to:

```text
Execution
Process
Thread
Concurrency
Parallelism
Coroutines
```

Almost every performance-related topic in Android eventually comes back to how CPU time is used.

---

## Revision Table

| Section | Content |
|----------|----------|
| Definition | Component responsible for executing instructions |
| Mental Model | Chef following a recipe |
| Main Responsibility | Execute instructions |
| Required For Execution | Yes |
| Limited Resource | Yes |
| Previous Concept | Program |
| Next Concept | Memory |
| Related Concepts | Execution, Process, Thread |

---

## Future Exploration

- CPU Cores
- CPU Scheduling
- Context Switching
- Multi-Core Processors
- CPU Architecture
- Performance Optimization
