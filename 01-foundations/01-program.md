# Program

## What Problem Were We Facing?

Before we can understand processes, threads, Android applications, or coroutines, we first need to understand what actually gets executed by a computer.

When developers write code, they create instructions that describe what a computer should do.

However, code written by a developer is not immediately running.

There must be a representation of those instructions that can be stored, distributed, and eventually executed.

This representation is called a **Program**.

Without programs, there would be nothing for a computer to execute.

---

## Definition

A program is a collection of instructions written to perform a specific task.

A program exists as passive code stored somewhere, such as:

- A file on disk
- An executable
- An installed application
- A compiled binary

A program by itself is not running.

It simply contains instructions that can be executed later.

---

## Mental Model

Think of a recipe book.

A recipe contains instructions:

```text
1. Cut vegetables
2. Heat oil
3. Cook vegetables
4. Serve
```

The recipe itself is not cooking anything.

It is simply a set of instructions waiting to be followed.

Similarly:

```text
Recipe
↓
Instructions waiting to be executed
```

is analogous to:

```text
Program
↓
Instructions waiting to be executed
```

---

## Program vs Running Program

One of the most important distinctions in computer science is:

```text
Program ≠ Running Program
```

A program stored on disk is passive.

When the operating system starts executing that program, it becomes a process.

Example:

```text
Chrome.exe
↓
Program
```

When opened:

```text
Running Chrome
↓
Process
```

Similarly:

```text
WhatsApp APK
↓
Program
```

When launched:

```text
Running WhatsApp
↓
Process
```

This distinction becomes extremely important when learning about processes and threads.

---

## Characteristics

A program:

- Contains instructions
- Is stored on disk
- Is passive
- Is not executing
- Can be executed multiple times

Multiple processes can be created from the same program.

Example:

```text
Program
↓
Chrome
```

May create:

```text
Chrome Process #1

Chrome Process #2

Chrome Process #3
```

depending on how the operating system manages execution.

---

## Why Is This Concept Important?

Many beginners incorrectly think:

```text
Program = Process
```

They are not the same thing.

Understanding the difference between:

```text
Program
↓
Stored instructions
```

and

```text
Process
↓
Executing program
```

is one of the foundational ideas of operating systems.

The next concept in the atlas builds directly on this distinction.

---

## Example

Consider an Android application:

```text
Instagram APK
```

When downloaded:

```text
Program
```

When opened:

```text
Android creates a process
↓
Application starts running
```

The APK itself is not executing.

The process created from it is.

---

## Revision Table

| Section | Content |
|----------|----------|
| Definition | A collection of instructions written to perform a task |
| Mental Model | Recipe book |
| Stored Where? | Disk / Executable / APK |
| Running? | No |
| Key Distinction | Program ≠ Process |
| Next Concept | Process |
| Related Concepts | Process, Execution, Operating System |

---

## Future Exploration

- Compilation
- Interpreted vs Compiled Languages
- Executables
- Binaries
- Operating Systems
