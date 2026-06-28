# Book 1 — Foundations

This book contains the foundational dependency chain for understanding Android execution, concurrency, reactive programming, state, and lifecycle.

## Included Documents

- [DOC 1 — Computer & Android Foundations](./01-foundations/)
- [DOC 2 — Coroutines Core](./02-coroutines-core/)
- [DOC 3 — Reactive Streams & Flow](./03-flow/)
- [DOC 4 — State Management](./04-state-management/)
- [DOC 5 — Android Lifecycle & Architecture](./05-android-lifecycle-architecture/)

---

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
