# DOC 5 — Android Lifecycle & Architecture

**Purpose:** Ownership & Data Flow

This document teaches the Android-specific architecture that brings together everything from DOC 1-4. It explains how screen lifecycle, state ownership, and data flow work in production Android apps.

## Concepts Included

### Part 1 — Application Ownership
- [Android Process](./01-android-process.md)
- [Application](./02-application.md)

### Part 2 — Screen Structure
- [Activity](./03-activity.md)
- [Fragment](./04-fragment.md)
- [FragmentManager & Transactions](./05-fragment-manager.md)

### Part 3 — Screen Lifetime
- [Lifecycle](./06-lifecycle.md)
- [Configuration Changes](./07-configuration-changes.md)

### Part 4 — Screen State Ownership
- [ViewModel](./08-viewmodel.md)
- [ViewModelStore](./09-viewmodel-store.md)
- [ViewModel Lifecycle](./10-viewmodel-lifecycle.md)
- [viewModelScope](./11-viewmodel-scope.md)
- [SavedStateHandle](./12-saved-state-handle.md)
- [ViewModel Owners](./13-viewmodel-owners.md)

### Part 5 — Data Ownership
- [Repository](./14-repository.md)
- [Single Source of Truth](./15-single-source-of-truth.md)

### Part 6 — Layers of State
- [UI State](./16-ui-state.md)
- [Business State](./17-business-state.md)

### Part 7 — Data Flow
- [Unidirectional Data Flow](./18-unidirectional-data-flow.md)

## Reading Order

Concepts should be read in the order listed because each builds on the previous:

1. **Understand containers** (Process, Application, Activity, Fragment)
2. **Understand screen lifetime** (Lifecycle, Configuration Changes)
3. **Learn about state ownership** (ViewModel, ViewModelStore, Lifecycle)
4. **Learn about data ownership** (Repository, Single Source of Truth)
5. **Understand state types** (UI State vs Business State)
6. **Put it together** (Unidirectional Data Flow)

## How This Fits Into The Atlas

This document answers: **How do Android applications organize screen state and data?**

It synthesizes everything from Books 1-4 into a coherent architecture. Every subsequent book builds on these principles.

The next book, **Book 2 — Architecture & UI**, teaches how to implement this architecture using Jetpack Compose.

---

**Start with:** [Android Process](./01-android-process.md)
