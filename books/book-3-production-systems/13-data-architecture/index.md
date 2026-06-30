# DOC 13 — Data Architecture

**Purpose:** How Production Applications Organize Data

This document synthesizes DOC 11 (Remote Communication) and DOC 12 (Local Persistence) into a complete data architecture. It's the full picture of how data flows from server to database to ViewModel to UI — and back.

## Concepts

### Part 1 — The Data Layer
- [x] [Why Data Architecture Exists](./01-why-data-architecture-exists.md) — Why separating data concerns from UI is essential in production apps
- [ ] The Data Layer — The architectural layer responsible for all data access and business data rules
- [ ] Data Source (concept) — A single interface to one data origin, either remote or local
- [ ] Remote Data Source (recap from DOC 11) — The class that talks to a network API
- [ ] Local Data Source — The class that talks to a local database or preference store

### Part 2 — The Repository
- [x] [Repository Pattern — The Full Picture](./06-repository-pattern.md) — How the repository coordinates remote and local data sources into one API
- [x] [Single Source of Truth](./07-single-source-of-truth.md) — Why all data should flow from one authoritative source

### Part 3 — Offline-First Patterns
- [x] [Offline-First Architecture](./08-offline-first-architecture.md) — Designing so the local database is primary and the network is a sync mechanism
- [ ] Cache-Then-Network Pattern — Serving cached data immediately while refreshing from the network
- [ ] Network-Then-Cache Pattern — Fetching from the network first and falling back to cache on failure

### Part 4 — Data Modeling
- [x] [Domain Model vs DTO vs Entity](./11-domain-model-vs-dto-vs-entity.md) — The three distinct data types and why each layer needs its own
- [ ] Data Mapping — Writing the mappers that translate between DTO, Entity, and Domain Model

### Part 5 — Advanced Patterns
- [ ] Use Case / Interactor — Encapsulating a single business operation into a callable class
- [ ] flowOn and Dispatcher Strategy in Data Layer — Choosing the right dispatcher for IO-bound operations in repositories
- [ ] Error Propagation Through Data Layers — How exceptions and failure states travel from data source to UI
- [ ] Repository Testing Strategy — How to test repositories using fakes and in-memory data sources
- [ ] Data Layer in Multi-Module Apps — Structuring data modules so feature modules stay decoupled
- [ ] Production Data Architecture Patterns — Patterns from real large-scale Android apps

## Reading Order

Concepts should be read in the order listed because:

1. **Understand why layers exist** (Why Data Architecture Exists, The Data Layer)
2. **Understand data sources** (Remote, Local — recap and deepen)
3. **Learn the Repository** (the coordinator that brings it all together)
4. **Learn caching strategies** (offline-first, cache-then-network)
5. **Learn data modeling** (the types that flow between layers)
6. **Master advanced patterns** (use cases, error propagation, multi-module)

## How This Fits Into The Atlas

This document answers: **How does data flow through an entire Android application?**

You first met the Repository in DOC 5 as a concept. Now you see the full implementation — how it coordinates remote APIs (DOC 11) and local databases (DOC 12) into a single source of truth that feeds ViewModels and ultimately the UI.

The next document, **DOC 14 — Dependency Injection**, teaches how all these layers get wired together in production.

---

**Start with:** [Why Data Architecture Exists](./01-why-data-architecture-exists.md)
