# DOC 9 — Production Compose Patterns

**Purpose:** How Experienced Android Engineers Design Compose Applications

This document teaches the practical patterns and techniques that experienced engineers use to build scalable, maintainable Compose applications. It synthesizes knowledge from DOC 1-7 into actionable architecture.

## Concepts Included

### Part 1 — Thinking In Recomposition
- [x] [Recomposition Boundaries](./01-recomposition-boundaries.md) — Where one recomposition scope ends and another begins
- [x] [Recomposition Cost](./02-recomposition-cost.md) — Why recomposition isn't free and what makes it expensive

### Part 2 — State Architecture
- [x] [Choosing State Ownership](./03-choosing-state-ownership.md) — When state belongs in a composable vs a ViewModel
- [x] [Stateless vs Stateful Composables](./04-stateless-vs-stateful-composables.md) — The trade-off between composability and convenience
- [x] [State Hoisting](./05-state-hoisting.md) — Moving state up to make composables reusable and testable
- [x] [State Ownership Patterns](./06-state-ownership-patterns.md) — Common patterns for organizing state in Compose screens

### Part 3 — Side Effects
- [x] [Why Side Effects Exist](./07-why-side-effects-exist.md) — Why Compose needs explicit side effect APIs rather than free-form imperative code
- [x] [LaunchedEffect](./08-launchedeffect.md) — Running a coroutine scoped to a composable's lifecycle
- [x] [Keys & Identity](./09-keys-and-identity.md) — How keys control when an effect restarts
- [x] [DisposableEffect](./10-disposableeffect.md) — Effects that need cleanup when the composable leaves the composition
- [x] [Synchronizing External State (SideEffect)](./11-publishing-compose-state-sideeffect.md) — Pushing Compose state into non-Compose code safely
- [x] [rememberCoroutineScope](./12-remembercoroutinescope.md) — Getting a coroutine scope that can be used in event handlers
- [x] [rememberUpdatedState](./13-rememberupdatedstate.md) — Capturing the latest value of a parameter inside a long-running effect

### Part 4 — State Transformation
- [x] [derivedStateOf](./14-derivedstateof.md) — Computing derived state that only triggers recomposition when the result changes
- [x] [produceState](./15-producestate.md) — Converting non-Compose async sources into Compose state
- [x] [snapshotFlow](./16-snapshotflow.md) — Converting Compose state reads into a Flow

### Part 5 — Lifecycle Integration
- [x] [collectAsStateWithLifecycle](./17-collectasstatewithlifecycle.md) — Collecting Flows in a lifecycle-aware way that pauses when the app backgrounds
- [x] [repeatOnLifecycle](./18-repeatonlifecycle.md) — Running a coroutine block only when the lifecycle is in a specific state
- [x] [Lifecycle-Aware Collection Patterns](./19-lifecycle-aware-collection-patterns.md) — Patterns for correctly integrating lifecycle state with Compose collection

### Part 6 — Compose Runtime Optimization
- [x] [Immutable Models](./20-immutable-models.md) — Why using immutable data classes makes composables skippable
- [x] [Stability Annotations](./21-stability-annotations-immutable-stable.md) — @Stable and @Immutable — telling the compiler what it can't infer
- [x] [Lazy List Item Keys](./22-lazy-list-item-keys.md) — Giving LazyColumn items stable identities to avoid unnecessary recomposition
- [x] [Lazy Lists](./23-lazy-lists.md) — How LazyColumn and LazyRow work and how to use them correctly
- [x] [Performance Anti-Patterns](./24-performance-anti-patterns.md) — Common Compose performance mistakes and how to recognize them

### Part 7 — Compose Architecture Patterns
- [x] [Screen Decomposition](./25-screen-decomposition.md) — Breaking large screens into composable units with clear responsibilities
- [x] [Reusable UI Components](./26-reusable-ui-components.md) — Designing composables that are flexible and reusable across screens
- [x] [Design System](./27-design-system.md) — Organizing colors, typography, and spacing into a coherent theme
- [x] [CompositionLocal](./28-composition-local.md) — Passing data implicitly down the composition tree without prop drilling
- [x] [Theming](./29-theming.md) — Implementing a full MaterialTheme-based theming system
- [x] [Folder Structure](./30-folder-structure.md) — How experienced teams organize large Compose codebases

## Reading Order

Concepts should be read in the order listed because:

1. **Understand performance** (Recomposition Boundaries, Cost)
2. **Design state architecture** (Ownership, Hoisting, Patterns)
3. **Handle side effects** (Why, LaunchedEffect, DisposableEffect)
4. **Transform state** (derivedStateOf, produceState, snapshotFlow)
5. **Integrate with lifecycle** (collectAsStateWithLifecycle, repeatOnLifecycle)
6. **Optimize runtime** (Immutability, Stability, Lazy Lists)
7. **Organize large apps** (Decomposition, Design Systems, Structure)

## How This Fits Into The Atlas

This document answers: **How do experienced engineers architect Compose applications?**

It takes the mental models and internals from DOC 6-7 and shows how to apply them in production code.

The next book, **Book 3 — Production Android**, teaches how to integrate Compose into complete production systems with networking, databases, and real-world complexity.

---

**Start with:** [Recomposition Boundaries](./01-recomposition-boundaries.md)
