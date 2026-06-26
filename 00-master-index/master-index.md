# Android Mental Model Atlas

**Version:** v1.0  
**Status:** Active

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

Purpose: How Data Flows

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

- [x] From Values To Flows

- [x] Keeping Only The Latest Result
    - flatMapLatest

- [x] Executing Flow Pipelines
    - Intermediate Operators
    - Terminal Operators

- [x] Flow In Android Architecture
---

## DOC 4 — State Management

Purpose: How State & Events Are Represented

- [x] State

- [x] State Holder

- [x] Characteristics Of State

- [x] State vs Event

- [x] Why StateFlow Exists

- [x] StateFlow

- [x] MutableStateFlow

- [x] StateFlow vs Flow

- [x] SharedFlow

- [x] MutableSharedFlow

- [x] Replay

- [x] Channel

- [x] stateIn

- [x] initialValue

- [x] SharingStarted
    - Eagerly
    - Lazily
    - WhileSubscribed

---

## DOC 5 — Android Lifecycle & Architecture

**Purpose:** Ownership & Data Flow

### Part 1 — Application Ownership

- [x] Android Process
- [x] Application

### Part 2 — Screen Structure

- [x] Activity
- [x] Fragment
- [x] FragmentManager & Transactions

### Part 3 — Screen Lifetime

- [x] Lifecycle
- [x] Configuration Changes

### Part 4 — Screen State Ownership

- [x] ViewModel

- [x] ViewModelStore
      - preserves ViewModels
      - lookup by key

- [x] ViewModel Lifecycle
      - why ViewModelStore survives recreation
      - Activity instance vs logical screen
      - when ViewModels are actually destroyed

- [x] viewModelScope
- [x] SavedStateHandle
- [x] ViewModel Owners

### Part 5 — Data Ownership

- [x] Repository
- [x] Single Source of Truth

### Part 6 — Layers of State

- [x] UI State
- [x] Business State

### Part 7 — Data Flow

- [x] Unidirectional Data Flow

---

## DOC 6 — Jetpack Compose

**Purpose:** How Modern Android UI Is Described And Updated

### Part 1 — A New Way Of Building UI

- [x] Imperative UI vs Declarative UI (includes UI = f(State))

### Part 2 — Composables

- [x] Composable
- [x] Composition
- [x] Composition Tree

### Part 3 — Recomposition

- [x] Recomposition
- [x] Recomposition Scope
- [x] Skipping Recomposition

### Part 4 — Local State

- [x] remember
- [x] mutableStateOf
- [x] Observable State`<T>`
- [x] MutableState<T>

### Part 5 — State Persistence

- [x] rememberSaveable

### Part 6 — ViewModel State In Compose

- [x] StateFlow → Compose
- [x] collectAsState()

### Part 7 — Choosing State Ownership

- [x] Local UI State
- [x] ViewModel UI State

---

## DOC 7 — Inside Compose Runtime

**Purpose:** How Compose Understands, Remembers And Updates Your UI

### Part 1 — Runtime Overview

- [x] From Composable Functions To Runtime
- [x] Composer
- [x] Composition Tree
- [x] UI Tree
- [x] Composition Tree vs UI Tree

### Part 2 — Remembering UI

- [x] Slot Table
- [x] Groups
- [x] Anchors
- [x] How remember Works
- [x] Key Changes

### Part 3 — State Observation

- [x] Snapshot System
- [x] SnapshotState
- [x] State Read Tracking
- [x] State Write Tracking

### Part 4 — Recomposition Internals

- [x] How Recomposition Works
- [x] Restart Groups
- [x] Skipping
- [x] Stability

### Part 5 — Rendering Pipeline

- [x] LayoutNode
- [x] Measure
- [x] Layout
- [x] Draw
- [x] Applier

---

## DOC 8 — Production Compose Patterns

**Purpose:** How Experienced Android Engineers Design Compose Applications

### Part 1 — Working With The Runtime

You've learned how the Compose Runtime works.

Now learn how to write code that works *with* the runtime instead of against it.

- [ ] Why Recomposition Is Cheap
- [ ] What Actually Gets Rebuilt
- [ ] Thinking In Recomposition
- [ ] Common Recomposition Myths

### Part 2 — Designing Stateful UI

Where should state actually live?

- [ ] Stateless vs Stateful Composables
- [ ] State Hoisting
- [ ] Choosing State Ownership
- [ ] State Hoisting Patterns

### Part 3 — Effects & Lifecycle

Composable functions are supposed to describe UI.

So where does real work happen?

- [ ] Why Side Effects Exist
- [ ] LaunchedEffect
- [ ] SideEffect
- [ ] DisposableEffect
- [ ] rememberCoroutineScope
- [ ] rememberUpdatedState

### Part 4 — Advanced State APIs

How do we bridge Compose with asynchronous and derived state?

- [ ] produceState
- [ ] derivedStateOf
- [ ] snapshotFlow

### Part 5 — Lifecycle Integration

Compose doesn't replace Android.

How should the two cooperate?

- [ ] collectAsStateWithLifecycle
- [ ] Lifecycle-aware Collection
- [ ] repeatOnLifecycle
- [ ] Compose Navigation Lifecycle (optional)

### Part 6 — Performance Engineering

How do experienced Compose developers write efficient UI?

- [ ] Keys
- [ ] Immutable Models
- [ ] Stability Annotations
- [ ] Lazy Lists
- [ ] Measuring Recomposition
- [ ] Performance Anti-patterns

### Part 7 — Scaling Compose

How do large teams organize thousands of composables?

- [ ] Screen Decomposition
- [ ] Reusable UI Components
- [ ] Design System
- [ ] Theming
- [ ] Folder Structure

---

## DOC 9 — Production Android Architecture

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

## DOC 10 — Performance, Debugging & Production Diagnostics

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
