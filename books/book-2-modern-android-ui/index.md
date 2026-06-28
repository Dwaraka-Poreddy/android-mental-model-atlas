# Book 2 — Modern Android UI

This book covers declarative UI, Compose runtime internals, and production-level Compose architecture.

## Included Documents

- [DOC 6 — Jetpack Compose](./06-jetpack-compose/)
- [DOC 7 — Inside Compose Runtime](./07-inside-compose-runtime/)
- [DOC 8 — Production Compose Patterns](./08-production-compose-patterns/)

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

### Part 1 — Thinking In Recomposition

**Purpose:** Develop the mental model experienced Compose developers use while reasoning about performance.

- [x] Recomposition Boundaries
- [x] Recomposition Cost

### Part 2 — State Architecture

**Purpose:** Learn how to decide where state belongs before writing any code.

- [x] Choosing State Ownership
- [x] Stateless vs Stateful Composables
- [x] State Hoisting
- [x] State Ownership Patterns

### Part 3 — Side Effects

**Purpose:** Learn how composables safely interact with the outside world.

- [x] Why Side Effects Exist
- [x] LaunchedEffect
- [x] Keys & Identity
- [x] DisposableEffect
- [x] Synchronizing External State (`SideEffect`)
- [x] rememberCoroutineScope
- [x] rememberUpdatedState

### Part 4 — State Transformation

**Purpose:** Learn how to derive, bridge, and transform state efficiently.

- [x] derivedStateOf
- [x] produceState
- [x] snapshotFlow

### Part 5 — Lifecycle Integration

**Purpose:** Learn how Compose cooperates with the Android lifecycle.

- [x] collectAsStateWithLifecycle
- [x] repeatOnLifecycle
- [x] Lifecycle aware Collection Patterns

### Part 6 — Compose Runtime Optimization

**Purpose:** Learn how the Compose Runtime minimizes work and how experienced engineers write highly efficient UI.

- [x] Immutable Models
- [x] Stability Annotations (`@Immutable`, `@Stable`)
- [x] Lazy List Item Keys
- [x] Lazy Lists
- [x] Performance Anti-patterns

### Part 7 — Compose Architecture Patterns

**Purpose:** Learn how large teams structure, reuse, and maintain Compose applications as projects grow.

- [x] Screen Decomposition
- [x] Reusable UI Components
- [x] Design System
- [x] CompositionLocal
- [x] Theming
- [x] Folder Structure
