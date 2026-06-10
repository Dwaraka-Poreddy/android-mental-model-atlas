# Process

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

Execution
↓
Instructions Being Performed
```

This naturally raises an important question:

```text
What do we call a program that is currently being executed?
```

When Instagram is installed on your phone, it exists as a program.

When you tap the Instagram icon and it starts running, something fundamentally changes.

The operating system allocates resources such as:

- CPU time
- Memory
- System resources

and begins execution.

This running instance is called a **Process**.

---

## Definition

A process is a program that is currently being executed.

In simple terms:

```text
Program
+
CPU
+
Memory
+
Execution
↓
Process
```

A process is the operating system's representation of a running program.

---

## Mental Model

Think about a recipe book.

The recipe itself is just instructions.

```text
Recipe
↓
Instructions
```

Now imagine a chef starts following the recipe.

The chef has:

```text
Ingredients

Utensils

Workspace

Cooking Activity
```

The cooking process is now actively happening.

Similarly:

```text
Program
↓
Instructions
```

becomes:

```text
Process
↓
Running Program
```

A process is like the cooking activity in progress.

---

## What Does A Process Contain?

A process is much more than code.

When the operating system creates a process, it allocates resources for it.

A process typically contains:

- Program code
- Memory
- Variables
- Objects
- Execution state
- Open files
- Network connections

Think of a process as a complete execution environment.

---

## Program vs Process

This distinction is one of the most important concepts in computing.

### Program

```text
Stored on Disk

Passive

Not Running
```

Examples:

```text
Instagram APK

Chrome.exe

WhatsApp APK
```

---

### Process

```text
Running

Uses CPU

Uses Memory

Has State
```

Examples:

```text
Running Instagram

Running Chrome

Running WhatsApp
```

---

## Lifecycle Of A Process

At a very high level:

```text
Program Exists
↓
User Launches Program
↓
Operating System Creates Process
↓
Process Runs
↓
Process Ends
```

After the process ends:

```text
Memory Released

Resources Freed
```

The original program still remains on disk.

---

## Multiple Processes From The Same Program

A single program can create multiple processes.

Example:

```text
Google Chrome
↓
Program
```

may result in:

```text
Chrome Process #1

Chrome Process #2

Chrome Process #3
```

Each process has:

- Its own memory
- Its own execution state
- Its own resources

Even though they originate from the same program.

---

## Android Example

Suppose Instagram is installed on your phone.

Before opening:

```text
Instagram APK
↓
Program
```

When you tap the icon:

```text
Android Operating System
↓
Creates Process
↓
Allocates Memory
↓
Starts Execution
```

Instagram is now a running process.

If Android later kills the app:

```text
Process Ends
↓
Resources Released
```

The APK still exists.

Only the process disappears.

---

## Why Is This Concept Important?

Many Android concepts are built on top of processes.

Examples:

- Android Application Process
- App Startup
- App Shutdown
- Memory Management
- Process Death
- Background Applications

Understanding processes helps answer questions such as:

```text
Why does an app consume RAM?

Why does Android kill background apps?

Why does reopening an app sometimes feel like a fresh launch?
```

All of these questions involve processes.

---

## A Process Is Not A Thread

A common misconception is:

```text
Process = Thread
```

This is incorrect.

A process can contain one or more threads.

Think of it as:

```text
Process
↓
Container

Threads
↓
Workers Inside The Container
```

We will explore threads in the next concept.

---

## Relationship With Future Concepts

A process is closely related to:

```text
Thread
Main Thread
Background Thread
Android Application Process
ViewModel Lifecycle
Process Death
```

Understanding processes is essential before learning threads.

---

## Revision Table

| Section | Content |
|----------|----------|
| Definition | A program that is currently executing |
| Mental Model | Cooking in progress |
| Created By | Operating System |
| Uses CPU | Yes |
| Uses Memory | Yes |
| Has State | Yes |
| Previous Concept | Execution |
| Next Concept | Thread |
| Related Concepts | Program, Thread, Operating System |

---

## Future Exploration

- Process Scheduling
- Process Priority
- Android Process Lifecycle
- Process Death
- Inter-Process Communication (IPC)
- Multi-Process Applications
