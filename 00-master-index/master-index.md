# Android Mental Model Atlas

**Version:** v1.0  
**Status:** Active

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

- [ ] Recomposition
- [ ] Recomposition Scope
- [ ] Skipping Recomposition

### Part 4 — Local State

- [ ] remember
- [ ] mutableStateOf
- [ ] State<T>
- [ ] MutableState<T>

### Part 5 — State Persistence

- [ ] rememberSaveable

### Part 6 — ViewModel State In Compose

- [ ] StateFlow → Compose
- [ ] collectAsState()

### Part 7 — Choosing State Ownership

- [ ] Local UI State
- [ ] ViewModel UI State

---

## DOC 7 — Inside Compose Runtime

**Purpose:** How Compose Understands, Remembers And Updates Your UI

### Part 1 — Runtime Overview

- [ ] From Composable Functions To Runtime
- [ ] Composer
- [ ] Composition Tree
- [ ] UI Tree
- [ ] Composition Tree vs UI Tree

### Part 2 — Remembering UI

- [ ] Slot Table
- [ ] Groups
- [ ] Anchors
- [ ] How remember Works
- [ ] Key Changes

### Part 3 — State Observation

- [ ] Snapshot System
- [ ] SnapshotState
- [ ] State Read Tracking
- [ ] State Write Tracking

### Part 4 — Recomposition Internals

- [ ] Recomposition Algorithm
- [ ] Restart Groups
- [ ] Skipping
- [ ] Stability
- [ ] Stability Inference

### Part 5 — Rendering Pipeline

- [ ] LayoutNode
- [ ] Measure
- [ ] Layout
- [ ] Draw
- [ ] Applier

### Part 6 — Runtime Performance

- [ ] Why Recomposition Is Cheap
- [ ] What Actually Gets Rebuilt
- [ ] Avoiding Unnecessary Work

---

## DOC 8 — Production Compose Patterns

**Purpose:** How Large Compose Applications Are Built

### Part 1 — State Architecture

- [ ] Stateless vs Stateful Composables
- [ ] State Hoisting
- [ ] Single Source Of Truth
- [ ] UDF In Compose

### Part 2 — Side Effects

- [ ] LaunchedEffect
- [ ] SideEffect
- [ ] DisposableEffect
- [ ] rememberCoroutineScope
- [ ] rememberUpdatedState

### Part 3 — State Helpers

- [ ] produceState
- [ ] derivedStateOf
- [ ] snapshotFlow

### Part 4 — Lifecycle Awareness

- [ ] collectAsStateWithLifecycle
- [ ] Lifecycle-aware Collection
- [ ] repeatOnLifecycle In Compose

### Part 5 — Compose Performance Patterns

- [ ] Keys
- [ ] Lazy Lists
- [ ] Stable Models
- [ ] Immutable Models
- [ ] Avoiding Unnecessary Recomposition

### Part 6 — Large Screen Design

- [ ] Screen Decomposition
- [ ] Reusable UI Components
- [ ] Design System
- [ ] Theming

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
