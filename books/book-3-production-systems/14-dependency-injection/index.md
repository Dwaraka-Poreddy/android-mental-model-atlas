# DOC 14 — Dependency Injection

**Purpose:** How Production Apps Wire Themselves Together

This document teaches why dependency injection exists, how Hilt works, and how production Android applications use DI to connect ViewModels, Repositories, Data Sources, and everything in between.

## Concepts

### Part 1 — The Problem
- [x] [Why Dependency Injection Exists](./01-why-dependency-injection-exists.md) — The coupling and testability problems that DI solves
- [x] [The Problem With Manual Construction](./02-the-problem-with-manual-construction.md) — Why manually building dependency trees breaks down at scale

### Part 2 — The Concept
- [ ] Dependency Injection (concept) — What DI actually means and the inversion of control principle
- [ ] Constructor Injection — The simplest and most recommended form of DI

### Part 3 — Hilt
- [x] [Hilt — Android's DI Framework](./05-hilt.md) — The Dagger-based DI library recommended by Google for Android
- [ ] @HiltAndroidApp — The application-level annotation that initializes Hilt's component hierarchy
- [ ] @AndroidEntryPoint — Marking Activities and Fragments to receive injected dependencies
- [x] [@Inject Constructor](./08-inject-constructor.md) — Telling Hilt how to construct a class automatically
- [x] [@HiltViewModel](./09-hilt-viewmodel.md) — Integrating ViewModels into Hilt's dependency graph

### Part 4 — Modules & Providers
- [ ] @Module and @InstallIn — Declaring where to install a set of dependency bindings in the component hierarchy
- [ ] @Provides — Teaching Hilt how to create types it cannot construct automatically
- [ ] @Binds — Binding an interface to its implementation without an instantiation step

### Part 5 — Scoping & Advanced
- [ ] Component Hierarchy & Scoping — How Hilt's component tree determines the lifetime of injected objects
- [ ] @Singleton vs @ViewModelScoped vs @ActivityScoped — Choosing the right scope for each dependency
- [ ] Qualifiers (@Named, custom) — Distinguishing between multiple bindings of the same type
- [ ] Production DI Patterns (multi-module, testing) — How large apps structure modules, fakes, and test components

## Reading Order

Concepts should be read in the order listed because:

1. **Understand the problem** (manual construction, dependency chains)
2. **Learn the concept** (DI as a pattern, constructor injection)
3. **Learn Hilt** (Android's DI framework — @HiltAndroidApp, @Inject, @HiltViewModel)
4. **Learn modules** (providing dependencies that can't be constructor-injected)
5. **Master scoping** (controlling object lifetime across components)

## How This Fits Into The Atlas

This document answers: **How do production Android apps wire their dependencies?**

After building all the layers — networking (DOC 11), persistence (DOC 12), architecture (DOC 13) — this document shows how Hilt connects them automatically. Every `@Inject constructor` and `@HiltViewModel` you see in production code traces back to concepts here.

The next document, **DOC 15 — Background Work**, teaches how apps perform work that must survive beyond the current screen.

---

**Start with:** [Why Dependency Injection Exists](./01-why-dependency-injection-exists.md)
