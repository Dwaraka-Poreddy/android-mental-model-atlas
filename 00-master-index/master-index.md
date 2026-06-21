# Android Mental Model Atlas

**Version:** v1.0  
**Status:** Active

---

# Vision

The Android Mental Model Atlas is a long-term knowledge base built from first principles.

Its purpose is not to serve as a collection of Android notes, but to build and preserve a deep understanding of Android development by documenting:

- What each concept is
- Why it exists
- What problem it solves
- How it relates to other concepts
- Common misconceptions
- Real-world usage patterns

The goal is to create a resource that remains useful years later for:

- Revising Android concepts
- Understanding architecture decisions
- Preparing for interviews
- Onboarding to new projects
- Rebuilding forgotten mental models

---

# Core Philosophy

Whenever a new concept is introduced, always ask:

1. What problem existed before this concept?
2. Why was this concept created?
3. What problem does it solve?
4. What concepts should be understood before learning it?
5. What concepts naturally come after it?

Understanding the problem is more important than memorizing the API.

---

# Documentation Principles

### Problem Before API

Always teach:

```text
Problem
↓
Concept
↓
API
```

Never introduce an API before the reader understands the problem it solves.

---

### One Chapter = One Idea

A chapter should teach one primary idea.

If a chapter starts teaching multiple major ideas, split it.

---

### Don't Reopen Solved Problems

Once a concept solves a problem:

```text
CoroutineScope
↓
Ownership
```

later chapters should build upon that solution rather than reintroducing the same problem.

---

### Every Section Must Earn Its Place

For every section ask:

```text
If I delete this section,

Does the reader lose
any understanding?
```

If the answer is:

```text
No
```

remove the section.

---

### Avoid Repetition Disguised As Explanation

A concept should not be re-explained unless new information is being introduced.

Prefer:

```text
Maximum Clarity
With Minimum Words
```

over exhaustive repetition.

---

### Explicit Abstraction Level

Every chapter should clearly state:

```text
What abstraction level
we are discussing
```

This prevents confusion when deeper implementation details exist but are intentionally deferred.

---

# Repository Entry Point

The recommended starting points for this repository are:

```text
README.md
↓
00-master-index/master-index.md
↓
Concept Files
```

---

# Repository Structure Philosophy

The atlas is organized into documents (folders) and concepts (files).

## Default Rule

```text
One Major Concept = One File
```

Examples:

```text
program.md
process.md
thread.md
coroutine.md
flow.md
stateflow.md
viewmodel.md
remember.md
```

Supporting concepts may be grouped inside a parent concept when they do not justify separate files.

Examples:

```text
sharingstarted.md
├─ Eagerly
├─ Lazily
└─ WhileSubscribed
```

```text
cancellation.md
├─ isActive
├─ ensureActive
└─ NonCancellable
```

---

# Linking Philosophy

Use relative links whenever possible.

Examples:

```markdown
[Flow](../03-flow/flow.md)

[StateFlow](../04-state-management/stateflow.md)

[ViewModel](../05-android-architecture/viewmodel.md)
```

The atlas should gradually evolve into a navigable knowledge graph rather than a collection of isolated notes.

---

# Status Tracking

The Master Index acts as the progress tracker for the entire atlas.

```text
[ ] Not Yet Written

[x] Completed
```

---

# Reading Order

Documents should generally be read in the order listed below because later concepts depend on earlier concepts.

The atlas is designed as a dependency graph rather than an alphabetical reference.

---

# Atlas Structure

## DOC 1 — Computer & Android Foundations

**Purpose:** Why Coroutines Exist

### Concepts

- [x] Program
- [x] CPU
- [x] Memory
- [x] Execution
- [x] Process

- [x] Thread
- [x] Thread Scheduling
- [x] Context Switching
- [x] Main Thread
- [x] Background Thread

- [x] Blocking
- [x] Non-Blocking

- [x] Concurrency
- [x] Parallelism

- [x] UI Freeze
- [x] ANR

- [x] MessageQueue
- [x] Looper
- [x] Handler

- [x] Traditional Thread Problems
- [x] Thread Cost
- [x] Thread Pool
- [x] Thread Communication Complexity
- [x] Callback Hell

- [x] Why Coroutines Were Needed

---

## DOC 2 — Coroutines Core

**Purpose:** How Coroutines Work

### Concepts

- [x] Coroutine
- [x] Coroutine vs Thread

- [x] Suspension
- [x] Resume
- [x] Suspension Point

- [x] suspend Function
- [x] Why suspend Exists
- [x] Continuation

- [x] Coroutine Builder

- [x] launch
- [x] async
- [x] await

- [x] CoroutineScope (Ownership & Lifecycle)
- [x] Job

- [x] Parent–Child Job Hierarchy
- [x] Structured Concurrency

- [x] coroutineScope Builder
- [x] runBlocking

- [x] SupervisorJob
- [x] supervisorScope

- [x] Dispatcher

- [x] Main Dispatcher
- [x] IO Dispatcher
- [x] Default Dispatcher
- [x] Unconfined Dispatcher

- [x] CPU Cores
- [x] Dispatcher Internals
    - Thread Pools
    - CPU Cores
    - Parallelism Limits
    - Thread Reuse
    - Why Default Uses CPU Count
    - Why IO Allows More Threads

- [x] Cancellation
- [x] Cancellation Checks
    - isActive
    - ensureActive
- [x] NonCancellable
    
- [x] CancellationException
- [x] Exception Handling
    - try-catch

- [x] Exception Propagation

- [x] CoroutineExceptionHandler

- [x] launch vs async Exceptions

---
## DOC 3 — Reactive Streams & Flow

Purpose: How Data Moves

- [x] Why Flow Exists

- [x] Flow

- [x] Flow vs Suspend Function

- [x] Cold Flow

- [x] Producing And Consuming Flow
    - emit
    - collect

- [x] Flow Pipeline
    - Producer
    - Upstream
    - Downstream
    - Collector

- [x] Flow Operators

- [x] Transforming Values
    - map

- [x] Filtering Values
    - filter

- [x] Reducing Noise
    - debounce

- [x] Combining Flows
    - combine
    - zip

- [ ] From Values To Flows
    - flatMapLatest

- [ ] Terminal Operators

- [ ] Flow in Real Applications 
---

## DOC 4 — State Management

**Purpose:** How State & Events Are Represented

### Concepts

- [ ] State
- [ ] State Holder

- [ ] StateFlow
- [ ] MutableStateFlow
- [ ] StateFlow vs Flow

- [ ] SharedFlow
- [ ] MutableSharedFlow
- [ ] Replay

- [ ] Channel
- [ ] Producer
- [ ] Consumer

- [ ] stateIn
- [ ] initialValue

- [ ] SharingStarted
- [ ] Eagerly
- [ ] Lazily
- [ ] WhileSubscribed

- [ ] State vs Event

- [ ] Navigation Events
- [ ] Toast Events
- [ ] Snackbar Events

---

## DOC 5 — Android Lifecycle & Architecture

**Purpose:** Who Owns What

### Concepts

- [ ] Android Process
- [ ] Application

- [ ] Activity
- [ ] Fragment

- [ ] Lifecycle

- [ ] Configuration Changes
- [ ] Rotation

- [ ] ViewModel
- [ ] ViewModelStore
- [ ] ViewModel Lifecycle

- [ ] viewModelScope

- [ ] Repository

- [ ] Single Source of Truth

- [ ] UI State
- [ ] Screen State
- [ ] Business State

- [ ] Unidirectional Data Flow

---

## DOC 6 — Compose Foundations

**Purpose:** How Modern Android UI Works

### Concepts

- [ ] Composable
- [ ] Declarative UI

- [ ] UI = f(State)

- [ ] Recomposition
- [ ] Recomposition Triggers

- [ ] remember

- [ ] mutableStateOf

- [ ] State
- [ ] MutableState

- [ ] rememberSaveable

- [ ] collectAsState

- [ ] StateFlow → Compose

- [ ] Local UI State
- [ ] ViewModel State

---

## DOC 7 — Advanced Compose

**Purpose:** How Compose Works In Production

### Concepts

- [ ] State Hoisting

- [ ] LaunchedEffect
- [ ] SideEffect
- [ ] DisposableEffect

- [ ] produceState
- [ ] derivedStateOf

- [ ] Stability

- [ ] Recomposition Optimization

- [ ] collectAsStateWithLifecycle

---

## DOC 8 — Production Android Architecture

**Purpose:** How Real Android Apps Are Built

### Concepts

- [ ] Retrofit

- [ ] DTO

- [ ] API Layer

- [ ] Repository Patterns

- [ ] Mapping

- [ ] Room

- [ ] Caching

- [ ] WorkManager

- [ ] Refresh Pattern

- [ ] Retry Pattern

- [ ] Search Pattern

- [ ] Pagination

- [ ] Offline Support

- [ ] End-to-End Data Flow

---

## DOC 9 — Performance, Debugging & Production Diagnostics

**Purpose:** How Real Apps Fail And How Engineers Investigate Them

### Concepts

- [ ] Performance

- [ ] CPU Usage
- [ ] Memory Usage
- [ ] Battery Usage

---

- [ ] Crash
- [ ] Exception
- [ ] Stacktrace

- [ ] Crash Debugging
- [ ] Crash Triage

---

- [ ] ANR Debugging

- [ ] ANR Reports
- [ ] Main Thread Analysis
- [ ] Thread Dumps

- [ ] Why ANRs Are Harder Than Crashes

---

- [ ] Flutter ANRs

- [ ] Android Stacktrace vs Flutter Stacktrace
- [ ] Flutter Engine Threads
- [ ] Method Channels

---

- [ ] Jank
- [ ] Frame Drops
- [ ] Rendering Performance

- [ ] Choreographer
- [ ] Frame Timeline

---

- [ ] Startup Performance

- [ ] Cold Start
- [ ] Warm Start
- [ ] Hot Start

---

- [ ] Memory Leaks

- [ ] Leak Detection
- [ ] LeakCanary

---

- [ ] StrictMode

- [ ] Main Thread Violations
- [ ] Disk Access Violations

---

- [ ] Logging

- [ ] Logcat
- [ ] Structured Logging

---

- [ ] Monitoring

- [ ] Crashlytics
- [ ] ANR Monitoring
- [ ] Performance Monitoring

---

- [ ] Profiling

- [ ] CPU Profiling
- [ ] Memory Profiling
- [ ] Network Profiling

---

- [ ] Perfetto
- [ ] Systrace

---

- [ ] Production Investigation Workflow

- [ ] Reproduce
- [ ] Isolate
- [ ] Root Cause Analysis
- [ ] Verification

---

- [ ] Real World Case Studies

- [ ] Production ANRs
- [ ] Production Crashes
- [ ] Performance Regressions
- [ ] Flutter-Specific Issues

---

# Future Expansion

The atlas is a living document.

Documents may be:

- Expanded
- Split into multiple documents
- Reorganized
- Reclassified

as understanding grows.

Potential future standalone documents:

- Navigation
- Room
- WorkManager
- Testing
- Dependency Injection & Hilt
- Compose Navigation
- Offline First Architecture
- Modularization
- Performance Optimization

This list is intentionally open-ended.
