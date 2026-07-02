# DOC 7 — Jetpack Compose

**Purpose:** How Modern Android UI Is Described And Updated

This document introduces Jetpack Compose and explains the fundamental shift from imperative to declarative UI. It teaches the mental models needed to work with Compose effectively.

## Concepts Included

### Part 1 — A New Way Of Building UI
- [x] [Imperative UI vs Declarative UI](./01-imperative-vs-declarative-ui.md) — The shift from imperative to declarative UI and what it changes about how you think

### Part 2 — Composables
- [x] [Composable](./02-composable.md) — Functions annotated with @Composable that describe a piece of UI
- [x] [Composition](./03-composition.md) — The in-memory tree of composable calls that describes the current UI
- [x] [Composition Tree](./04-composition-tree.md) — The structure Compose builds from composable calls and how it maps to nodes

### Part 3 — Recomposition
- [x] [Recomposition](./05-recomposition.md) — How Compose re-runs composables when observed state changes
- [x] [Recomposition Scope](./06-recomposition-scope.md) — The boundary within which Compose decides what to re-run
- [x] [Skipping Recomposition](./07-skipping-recomposition.md) — How Compose avoids re-running composables whose inputs haven't changed

### Part 4 — Local State
- [x] [remember](./08-remember.md) — Storing a value that survives recomposition within a composable
- [x] [mutableStateOf](./09-mutable-state-of.md) — Creating observable state that triggers recomposition on write
- [x] [Observable State](./10-observable-state-state-t.md) — How Compose tracks reads of State<T> to know exactly what to recompose
- [x] [MutableState<T>](./11-mutable-state.md) — The mutable state holder returned by mutableStateOf

### Part 5 — State Persistence
- [x] [rememberSaveable](./12-remember-saveable.md) — Surviving not just recomposition but also process death and Activity recreation

### Part 6 — ViewModel State In Compose
- [x] [StateFlow → Compose](./13-stateflow-to-compose.md) — Bridging ViewModel StateFlow into Compose's state system
- [x] [collectAsState()](./14-collect-as-state.md) — Converting a Flow into Compose-observable State<T>

### Part 7 — Choosing State Ownership
- [x] [Local UI State](./15-local-ui-state.md) — When state belongs inside a composable rather than a ViewModel
- [x] [ViewModel UI State](./16-viewmodel-ui-state.md) — When state belongs in a ViewModel and how to wire it to Compose

## Reading Order

Concepts should be read in the order listed because:

1. **Understand the paradigm shift** (Imperative vs Declarative)
2. **Understand Compose structure** (Composable, Composition, Tree)
3. **Understand updates** (Recomposition, Scope, Skipping)
4. **Manage local state** (remember, mutableStateOf)
5. **Persist state** (rememberSaveable)
6. **Connect to ViewModel** (StateFlow, collectAsState())
7. **Choose where state lives** (Local vs ViewModel)

## How This Fits Into The Atlas

This document answers: **How does Compose change how we build Android UI?**

It builds on the architecture foundation from DOC 5, showing how Compose implements those patterns more elegantly.

The next document, **DOC 7 — Inside Compose Runtime**, teaches how Compose actually works internally.

---

**Start with:** [Imperative UI vs Declarative UI](./01-imperative-vs-declarative-ui.md)
