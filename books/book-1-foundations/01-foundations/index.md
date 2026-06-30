# DOC 1 — Computer & Android Foundations

**Purpose:** Why Coroutines Exist

This document covers the foundational computer science concepts that explain why Android needed coroutines. It builds the mental models necessary to understand concurrency, threading, and the constraints they solve.

## Concepts Included

- [Program](./01-program.md)
- [CPU](./02-cpu.md)
- [Memory](./03-memory.md)
- [Execution](./04-execution.md)
- [Process](./05-process.md)
- [Thread](./06-thread.md)
- [Thread Scheduling](./07-thread-scheduling.md)
- [Context Switching](./08-context-switching.md)
- [Main Thread](./09-main-thread.md)
- [Background Thread](./10-background-thread.md)
- [Blocking](./11-blocking.md)
- [Non-Blocking](./12-non-blocking.md)
- [Concurrency](./13-concurrency.md)
- [Parallelism](./14-parallelism.md)
- [UI Freeze](./15-ui-freeze.md)
- [ANR](./16-anr.md)
- [MessageQueue](./17-message-queue.md)
- [Looper](./18-looper.md)
- [Handler](./19-handler.md)
- [Traditional Thread Problems](./20-traditional-thread-problems.md)
- [Thread Cost](./21-thread-cost.md)
- [Thread Pools](./22-thread-pools.md)
- [Thread Communication Complexity](./23-thread-communication-complexity.md)
- [Callback Hell](./24-callback-hell.md)
- [Why Coroutines Were Needed](./25-why-coroutines-were-needed.md)

## Reading Order

Concepts should be read in the order listed above because each concept builds on previous knowledge:

1. Start with computer fundamentals (Program → CPU → Memory → Execution)
2. Understand what a process is
3. Learn about threads and threading
4. Understand the problems threads create
5. See why coroutines were needed as a solution

## How This Fits Into The Atlas

This document answers the question: **Why do Android developers use coroutines instead of threads?**

By the end of this document, you'll understand the fundamental constraints that make coroutines necessary. This mental model enables you to understand every subsequent coroutine concept.

The next document, **DOC 2 — Coroutines Core**, assumes you understand these foundations and teaches you how coroutines actually work.

---

**Start with:** [Program](./01-program.md)
