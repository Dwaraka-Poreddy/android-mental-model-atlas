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

- [ ] Program
- [ ] CPU
- [ ] Memory
- [ ] Execution
- [ ] Process

- [ ] Thread
- [ ] Main Thread
- [ ] Background Thread
- [ ] Context Switching

- [ ] Blocking
- [ ] Non-Blocking

- [ ] Concurrency
- [ ] Parallelism

- [ ] UI Freeze
- [ ] ANR

- [ ] Traditional Thread Problems
- [ ] Thread Cost
- [ ] Callback Hell

- [ ] Why Coroutines Were Needed

---

## DOC 2 — Coroutines Core

**Purpose:** How Coroutines Work

### Concepts

- [ ] Coroutine
- [ ] Suspension
- [ ] Resume
- [ ] Suspension Point

- [ ] suspend Function
- [ ] Continuation
- [ ] Why suspend Exists

- [ ] launch
- [ ] async
- [ ] await
- [ ] runBlocking
- [ ] coroutineScope

- [ ] CoroutineScope
- [ ] Job
- [ ] Parent Job
- [ ] Child Job

- [ ] Structured Concurrency
- [ ] supervisorScope
- [ ] SupervisorJob

- [ ] Main Dispatcher
- [ ] IO Dispatcher
- [ ] Default Dispatcher
- [ ] Unconfined Dispatcher

- [ ] Cancellation
- [ ] isActive
- [ ] ensureActive
- [ ] NonCancellable

- [ ] try-catch
- [ ] CoroutineExceptionHandler
- [ ] launch vs async Exceptions

---

## DOC 3 — Reactive Streams & Flow

**Purpose:** How Data Moves

### Concepts

- [ ] Flow
- [ ] Cold Flow

- [ ] emit
- [ ] collect
- [ ] Collector

- [ ] map
- [ ] filter
- [ ] debounce
- [ ] combine
- [ ] zip
- [ ] flatMapLatest

- [ ] Upstream
- [ ] Downstream

- [ ] Intermediate Operators
- [ ] Terminal Operators

- [ ] Flow Use Cases

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

This list is intentionally open-ended.- [ ] Traditional Thread Problems

- [ ] Thread Cost

- [ ] Callback Hell

- [ ] Why Coroutines Were Needed

---

DOC 2 — Coroutines Core

Purpose: How Coroutines Work

Concepts

- [ ] Coroutine

- [ ] Suspension

- [ ] Resume

- [ ] Suspension Point

- [ ] suspend Function

- [ ] Continuation

- [ ] Why suspend Exists

- [ ] launch

- [ ] async

- [ ] await

- [ ] runBlocking

- [ ] coroutineScope

- [ ] CoroutineScope

- [ ] Job

- [ ] Parent Job

- [ ] Child Job

- [ ] Structured Concurrency

- [ ] supervisorScope

- [ ] SupervisorJob

- [ ] Main Dispatcher

- [ ] IO Dispatcher

- [ ] Default Dispatcher

- [ ] Unconfined Dispatcher

- [ ] Cancellation

- [ ] isActive

- [ ] ensureActive

- [ ] NonCancellable

- [ ] try-catch

- [ ] CoroutineExceptionHandler

- [ ] launch vs async Exceptions

---

DOC 3 — Reactive Streams & Flow

Purpose: How Data Moves

Concepts

- [ ] Flow

- [ ] Cold Flow

- [ ] emit

- [ ] collect

- [ ] Collector

- [ ] map

- [ ] filter

- [ ] debounce

- [ ] combine

- [ ] zip

- [ ] flatMapLatest

- [ ] Upstream

- [ ] Downstream

- [ ] Intermediate Operators

- [ ] Terminal Operators

- [ ] Flow Use Cases

---

DOC 4 — State Management

Purpose: How State & Events Are Represented

Concepts

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

DOC 5 — Android Lifecycle & Architecture

Purpose: Who Owns What

Concepts

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

DOC 6 — Compose Foundations

Purpose: How Modern Android UI Works

Concepts

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

DOC 7 — Advanced Compose

Purpose: How Compose Works In Production

Concepts

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

DOC 8 — Production Android Architecture

Purpose: How Real Android Apps Are Built

Concepts

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

Future Expansion

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
---

Atlas Structure

DOC 1 — Computer & Android Foundations

Purpose: Why Coroutines Exist

Concepts

- [ ] Program

- [ ] CPU

- [ ] Memory

- [ ] Execution

- [ ] Process

- [ ] Thread

- [ ] Main Thread

- [ ] Background Thread

- [ ] Context Switching

- [ ] Blocking

- [ ] Non-Blocking

- [ ] Concurrency

- [ ] Parallelism

- [ ] UI Freeze

- [ ] ANR

- [ ] Traditional Thread Problems

- [ ] Thread Cost

- [ ] Callback Hell

- [ ] Why Coroutines Were Needed

---

DOC 2 — Coroutines Core

Purpose: How Coroutines Work

Concepts

- [ ] Coroutine

- [ ] Suspension

- [ ] Resume

- [ ] Suspension Point

- [ ] suspend Function

- [ ] Continuation

- [ ] Why suspend Exists

- [ ] launch

- [ ] async

- [ ] await

- [ ] runBlocking

- [ ] coroutineScope

- [ ] CoroutineScope

- [ ] Job

- [ ] Parent Job

- [ ] Child Job

- [ ] Structured Concurrency

- [ ] supervisorScope

- [ ] SupervisorJob

- [ ] Main Dispatcher

- [ ] IO Dispatcher

- [ ] Default Dispatcher

- [ ] Unconfined Dispatcher

- [ ] Cancellation

- [ ] isActive

- [ ] ensureActive

- [ ] NonCancellable

- [ ] try-catch

- [ ] CoroutineExceptionHandler

- [ ] launch vs async Exceptions

---

DOC 3 — Reactive Streams & Flow

Purpose: How Data Moves

Concepts

- [ ] Flow

- [ ] Cold Flow

- [ ] emit

- [ ] collect

- [ ] Collector

- [ ] map

- [ ] filter

- [ ] debounce

- [ ] combine

- [ ] zip

- [ ] flatMapLatest

- [ ] Upstream

- [ ] Downstream

- [ ] Intermediate Operators

- [ ] Terminal Operators

- [ ] Flow Use Cases

---

DOC 4 — State Management

Purpose: How State & Events Are Represented

Concepts

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

DOC 5 — Android Lifecycle & Architecture

Purpose: Who Owns What

Concepts

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

DOC 6 — Compose Foundations

Purpose: How Modern Android UI Works

Concepts

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

DOC 7 — Advanced Compose

Purpose: How Compose Works In Production

Concepts

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

DOC 8 — Production Android Architecture

Purpose: How Real Android Apps Are Built

Concepts

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

Future Expansion

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
