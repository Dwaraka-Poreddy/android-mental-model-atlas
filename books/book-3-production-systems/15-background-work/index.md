# DOC 15 — Background Work

**Purpose:** How Applications Continue Working After the UI Disappears

This document teaches how Android applications perform work that must survive beyond the current screen — syncing data, uploading files, running periodic cleanup — even when the user isn't looking at the app.

## Concepts

### Part 1 — The Problem
- [x] [Why Background Work Exists](./01-why-background-work-exists.md) — Why apps need to do work outside the UI lifecycle
- [ ] The Problem With Coroutines Alone — Why coroutines are cancelled when the app goes to background and what that means

### Part 2 — WorkManager
- [x] [WorkManager](./03-workmanager.md) — Android's library for guaranteed, constraint-aware background work
- [ ] Worker — The base class for synchronous background work units
- [x] [CoroutineWorker](./05-coroutine-worker.md) — The coroutine-friendly worker for async background tasks
- [ ] WorkRequest (OneTime vs Periodic) — Scheduling a task to run once or on a repeating schedule
- [x] [Constraints](./07-constraints.md) — Conditions that must be met before a work request executes

### Part 3 — Work Orchestration
- [ ] Work Chaining — Running workers in sequence or parallel with automatic data passing
- [ ] Work States & Observation — Tracking and observing the lifecycle of a scheduled work request
- [ ] ExistingWorkPolicy — Controlling what happens when duplicate work is enqueued
- [ ] Input & Output Data — Passing data into workers and reading results back out
- [ ] Retry & Backoff — Configuring how WorkManager retries failed work with exponential backoff

### Part 4 — Production Patterns
- [ ] Foreground Services vs WorkManager — When to use each and how they relate
- [ ] Production Background Patterns — Sync strategies, upload queues, and real-world WorkManager usage

## Reading Order

Concepts should be read in the order listed because:

1. **Understand why** (coroutines alone aren't enough for guaranteed work)
2. **Learn WorkManager** (the recommended API)
3. **Learn orchestration** (chaining, states, retry)
4. **Master production patterns** (when to use what)

## How This Fits Into The Atlas

This document answers: **How do apps do work even when the user isn't looking?**

After learning how to fetch data (DOC 11), store it locally (DOC 12), organize it (DOC 13), and wire it together (DOC 14), this document completes Book 3 by showing how work can happen reliably in the background.

---

**Start with:** [Why Background Work Exists](./01-why-background-work-exists.md)
