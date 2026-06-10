# Memory

## What Problem Were We Facing?

In the previous concept, we learned that the CPU executes instructions.

However, executing instructions is only part of the story.

Consider a simple calculation:

```text
10 + 20
```

The CPU needs somewhere to store:

- The value 10
- The value 20
- The result 30

Similarly, when Instagram is running, it needs somewhere to store:

- User information
- Posts
- Images
- Messages
- Application state

This raises an important question:

```text
Where does a running program store its working data?
```

The answer is **Memory**.

---

## Definition

Memory is the temporary storage area used by running programs to store data while they are executing.

A running program uses memory to store:

- Variables
- Objects
- Images
- Application state
- Intermediate calculations

Unlike disk storage, memory is designed for fast access.

---

## Mental Model

Think of a chef preparing food.

The recipe book contains instructions.

```text
Recipe
↓
Instructions
```

The chef executes those instructions.

```text
Chef
↓
Performs Work
```

But the chef also needs a working table.

```text
Vegetables
Spices
Utensils
Ingredients
```

must be placed somewhere while cooking.

That working table is analogous to memory.

```text
CPU
↓
Chef

Memory
↓
Working Table
```

The CPU performs the work.

Memory holds the data being worked on.

---

## Why Not Store Everything On Disk?

Programs are stored on disk.

Examples:

```text
Instagram APK

Chrome.exe

Photos
```

However, disk storage is much slower than memory.

If every operation required reading directly from disk:

```text
Open App
↓
Read From Disk
↓
Read Again
↓
Read Again
```

the application would become extremely slow.

Instead:

```text
Disk
↓
Load Into Memory
↓
CPU Uses Memory
```

This is significantly faster.

---

## Memory Is Temporary

One of the most important properties of memory is:

```text
Memory is temporary.
```

When a process ends:

```text
Process Stops
↓
Memory Released
```

The stored data disappears.

Example:

```text
Instagram Running
↓
Uses Memory

Instagram Closed
↓
Memory Freed
```

This is different from disk storage.

Disk storage remains even after the application closes.

---

## Android Example

Suppose you open Instagram.

Android loads:

- UI components
- Images
- User profile
- Feed data

into memory.

While the app is running:

```text
CPU
↓
Processes Data

Memory
↓
Stores Data
```

When Android decides to kill the process:

```text
Process Ends
↓
Memory Released
```

---

## Why Is Memory Important?

Almost every Android concept eventually involves memory.

Examples:

- Processes use memory
- Threads access memory
- Objects live in memory
- ViewModels store data in memory
- Compose state exists in memory

Understanding memory helps answer questions such as:

```text
Why do apps consume RAM?

Why does Android kill background apps?

Why can applications crash with OutOfMemoryError?
```

---

## CPU vs Memory

A useful distinction:

### CPU

```text
Executes Instructions
```

### Memory

```text
Stores Working Data
```

Think of it as:

```text
CPU
↓
Worker

Memory
↓
Workspace
```

The CPU performs work.

Memory stores the information needed for that work.

---

## Relationship With Future Concepts

Memory is closely related to:

```text
Execution
Process
Thread
Objects
Garbage Collection
ViewModel
Compose State
```

Many Android performance problems are ultimately related to memory usage.

---

## Revision Table

| Section | Content |
|----------|----------|
| Definition | Temporary storage used by running programs |
| Mental Model | Chef's working table |
| Purpose | Store working data |
| Permanent? | No |
| Released When | Process ends |
| Previous Concept | CPU |
| Next Concept | Execution |
| Related Concepts | Process, Thread, RAM |

---

## Future Exploration

- RAM
- Heap Memory
- Stack Memory
- Garbage Collection
- Memory Leaks
- OutOfMemoryError
- Android Memory Management
