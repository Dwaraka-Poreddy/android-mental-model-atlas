# Execution

## What Problem Were We Facing?

So far we have learned:

```text
Program
↓
Contains Instructions

CPU
↓
Executes Instructions

Memory
↓
Stores Working Data
```

However, we have been casually using the word "execute" without formally understanding what it means.

This raises an important question:

```text
What does it actually mean for a program to execute?
```

What is happening inside the computer when we say:

```text
The program is running
```

Understanding execution is important because many future concepts depend on it:

- Processes
- Threads
- Coroutines
- Concurrency
- Parallelism

All of them are fundamentally about execution.

---

## Definition

Execution is the process of a CPU carrying out instructions from a program.

When execution begins:

```text
Program Instructions
↓
CPU Reads Them
↓
CPU Performs Them
```

The instructions are no longer passive code.

They are actively being carried out.

---

## Mental Model

Imagine a recipe book.

The recipe contains instructions:

```text
1. Cut vegetables
2. Heat oil
3. Cook vegetables
```

The recipe itself is passive.

Nothing happens until the chef starts following the instructions.

The moment the chef begins:

```text
Read Instruction
↓
Perform Action
↓
Read Next Instruction
↓
Perform Action
```

cooking has started.

This is analogous to execution.

```text
Program
↓
Instructions

CPU
↓
Performs Instructions

Execution
↓
Work In Progress
```

---

## What Happens During Execution?

At a high level, the CPU repeatedly performs a cycle:

```text
Fetch Instruction
↓
Interpret Instruction
↓
Execute Instruction
↓
Move To Next Instruction
```

For example:

```text
a = 10

b = 20

c = a + b
```

The CPU executes many small operations behind the scenes:

```text
Load Value

Store Value

Add Numbers

Store Result
```

Even simple programs may require millions of instructions.

---

## Execution Is Sequential By Default

A common misconception is that programs do everything at once.

By default, instructions are executed one after another.

Example:

```text
Step 1
↓
Step 2
↓
Step 3
↓
Step 4
```

This is called sequential execution.

The CPU finishes one instruction before moving to the next.

Later, when we learn about threads and coroutines, we will explore ways to manage multiple tasks efficiently.

---

## Why Execution Matters

Execution is the moment when a program becomes useful.

Without execution:

```text
Program
↓
Just Instructions
```

With execution:

```text
Program
↓
Performs Work
```

Examples:

```text
Opening an App

Sending a Message

Playing a Video

Calculating a Result

Loading a Screen
```

All of these require execution.

---

## Android Example

Suppose you tap the Instagram icon.

The operating system begins executing instructions that:

```text
Create Application

Initialize Components

Load Feed

Render UI
```

These instructions are actively carried out by the CPU.

This ongoing activity is execution.

---

## Why Is This Concept Important?

Many future Android topics revolve around controlling execution.

Examples:

```text
Thread
↓
Unit Of Execution

Coroutine
↓
Lightweight Unit Of Execution

Concurrency
↓
Managing Multiple Executions

Parallelism
↓
Executing Multiple Tasks Simultaneously
```

Without understanding execution, these concepts can feel abstract.

---

## Program vs Execution

A useful distinction:

### Program

```text
Instructions
```

### Execution

```text
Instructions Being Performed
```

Think of it as:

```text
Recipe
↓
Instructions

Cooking
↓
Execution
```

The recipe and the cooking process are not the same thing.

Similarly:

```text
Program ≠ Execution
```

---

## Relationship With Future Concepts

Execution is closely related to:

```text
Process
Thread
Concurrency
Parallelism
Coroutines
Scheduling
```

Nearly every performance-related topic in Android is ultimately about managing execution.

---

## Revision Table

| Section | Content |
|----------|----------|
| Definition | CPU carrying out program instructions |
| Mental Model | Chef following a recipe |
| Requires | CPU + Program |
| Uses | Memory |
| Default Behavior | Sequential |
| Previous Concept | Memory |
| Next Concept | Process |
| Related Concepts | Thread, Coroutines, Concurrency |

---

## Future Exploration

- Instruction Cycle
- CPU Scheduling
- Threads
- Concurrency
- Parallelism
- Coroutines
- Operating System Scheduling
