# DOC 2 — Coroutines Core

**Purpose:** How Coroutines Work

This document teaches the mental models behind coroutines. It explains what coroutines are, how they're different from threads, and how they solve the problems identified in DOC 1.

## Concepts Included

- [Coroutine](./01-coroutine.md)
- [Coroutine vs Thread](./02-coroutine-vs-thread.md)
- [Suspension](./03-suspension.md)
- [Resume](./04-resume.md)
- [Suspension Point](./05-suspension-point.md)
- [Suspend Function](./06-suspend-function.md)
- [Why Suspend Exists](./07-why-suspend-exists.md)
- [Continuation](./08-continuation.md)
- [Coroutine Builder](./09-coroutine-builder.md)
- [Launch](./10-launch.md)
- [Async](./11-async.md)
- [Await](./12-await.md)
- [CoroutineScope](./13-coroutine-scope.md)
- [Job](./14-job.md)
- [Parent–Child Job Hierarchy](./15-parent-child-job-hierarchy.md)
- [Structured Concurrency](./16-structured-concurrency.md)
- [coroutineScope Builder](./17-coroutine-scope-builder.md)
- [runBlocking](./18-run-blocking.md)
- [SupervisorJob](./19-supervisor-job.md)
- [supervisorScope](./20-supervisor-scope.md)
- [Dispatcher](./21-dispatcher.md)
- [Main Dispatcher](./22-main-dispatcher.md)
- [IO Dispatcher](./23-io-dispatcher.md)
- [Default Dispatcher](./24-default-dispatcher.md)
- [Unconfined Dispatcher](./25-unconfined-dispatcher.md)
- [CPU Cores](./26-cpu-cores.md)
- [Dispatcher Internals](./27-dispatcher-internals.md)
- [Cancellation](./28-cancellation.md)
- [Cancellation Checks](./29-cancellation-checks.md)
- [isActive](./30-is-active.md)
- [ensureActive](./31-ensure-active.md)
- [NonCancellable](./32-non-cancellable.md)
- [CancellationException](./33-cancellation-exception.md)
- [Exception Handling](./34-exception-handling.md)
- [Exception Propagation](./35-exception-propagation.md)
- [CoroutineExceptionHandler](./36-coroutine-exception-handler.md)
- [Launch vs Async Exceptions](./37-launch-vs-async-exceptions.md)

## Reading Order

Concepts should be read in the order listed because each builds on the previous:

1. **What are coroutines?** (Coroutine, vs Thread, Suspension)
2. **How do they suspend and resume?** (Suspension Point, Continuation)
3. **How do you create them?** (Coroutine Builders: launch, async)
4. **How do you manage their lifetime?** (CoroutineScope, Job, Structured Concurrency)
5. **How do you control where they run?** (Dispatchers)
6. **How do you stop them?** (Cancellation)
7. **How do you handle errors?** (Exception Handling)

## How This Fits Into The Atlas

This document answers: **How do coroutines actually work?**

It builds on the foundations from DOC 1, showing how coroutines solve threading problems through suspension and resumption.

The next document, **DOC 3 — Reactive Streams & Flow**, builds on coroutines to teach how data flows through asynchronous systems.

---

**Start with:** [Coroutine](./01-coroutine.md)
