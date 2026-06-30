# Android Mental Model Atlas — Master Index

Everything in this atlas, in reading order.

Start with Book 1 and follow each link in sequence. Each concept builds on the previous.

---

## Book 1 — Foundations

**Goal:** Understand why Android needs coroutines, flows, and state management.

### DOC 1 — Computer & Android Foundations
> *Why Coroutines Exist*

- Program, CPU, Memory, Execution
- Process, Thread, Thread Scheduling, Context Switching
- Main Thread, Background Thread
- Blocking, Non-Blocking
- Concurrency, Parallelism
- UI Freeze, ANR
- MessageQueue, Looper, Handler
- Traditional Thread Problems, Thread Cost, Thread Pool, Callback Hell
- Why Coroutines Were Needed

→ [`books/book-1-foundations/01-foundations/`](./books/book-1-foundations/01-foundations/)

---

### DOC 2 — Coroutines Core
> *How Coroutines Work*

- Coroutine, Coroutine vs Thread
- Suspension, Resume, Suspension Point
- suspend Function, Why suspend Exists, Continuation
- Coroutine Builders: launch, async, await
- CoroutineScope, Job, Parent–Child Job Hierarchy
- Structured Concurrency
- coroutineScope Builder, runBlocking
- SupervisorJob, supervisorScope
- Dispatchers: Main, IO, Default, Unconfined
- Dispatcher Internals (thread pools, CPU cores, parallelism limits)
- Cancellation, isActive, ensureActive, NonCancellable
- Exception Handling, Exception Propagation
- CoroutineExceptionHandler

→ [`books/book-1-foundations/02-coroutines-core/`](./books/book-1-foundations/02-coroutines-core/)

---

### DOC 3 — Reactive Streams & Flow
> *How Data Flows*

- Why Flow Exists
- Flow, Flow vs Suspend Function, Cold Flow
- emit, collect, Flow Pipeline (producer → upstream → downstream → collector)
- Flow Operators: map, filter, debounce, combine, zip
- flatMapLatest
- Intermediate vs Terminal Operators
- Flow in Android Architecture

→ [`books/book-1-foundations/03-flow/`](./books/book-1-foundations/03-flow/)

---

### DOC 4 — State Management
> *How State & Events Are Represented*

- State, State Holder, Characteristics of State
- State vs Event
- Why StateFlow Exists
- StateFlow, MutableStateFlow, StateFlow vs Flow
- SharedFlow, MutableSharedFlow, Replay
- Channel
- stateIn, initialValue, SharingStarted (Eagerly, Lazily, WhileSubscribed)

→ [`books/book-1-foundations/04-state-management/`](./books/book-1-foundations/04-state-management/)

---

## Book 2 — Architecture & UI

**Goal:** Understand how Android apps are structured and how to build production UIs with Compose.

### DOC 5 — Android Lifecycle & Architecture
> *Ownership & Data Flow*

- Android Process, Application
- Activity, Fragment, FragmentManager & Transactions
- Lifecycle, Configuration Changes
- ViewModel, ViewModelStore, ViewModel Lifecycle, viewModelScope, SavedStateHandle, ViewModel Owners
- Repository, Single Source of Truth
- UI State, Business State
- Unidirectional Data Flow

→ [`books/book-1-foundations/05-android-lifecycle-architecture/`](./books/book-1-foundations/05-android-lifecycle-architecture/)

---

### DOC 6 — Navigation
> *How Screens Connect*

- Why navigation exists — the spaghetti-screen coupling problem
- Navigation Graph: the blueprint of all screens and their connections
- NavController: the driver that navigates, pops, and manages the back stack
- NavHost: where the current destination renders in your Compose UI
- Type-safe navigation with Compose, nested graphs, conditional navigation (auth gates)

→ [`books/book-2-architecture-and-ui/06-navigation/`](./books/book-2-architecture-and-ui/06-navigation/)

---

### DOC 7 — Jetpack Compose
> *How to Build UIs with Compose*

- Declarative UI paradigm
- Composable functions
- Composition and Recomposition
- State in Compose (remember, rememberSaveable)
- Compose state hoisting
- Modifiers, Layout, Row, Column, Box
- LazyColumn, LazyRow
- Navigation in Compose

→ [`books/book-2-architecture-and-ui/07-jetpack-compose/`](./books/book-2-architecture-and-ui/07-jetpack-compose/)

---

### DOC 8 — Inside Compose Runtime
> *How Compose Works Internally*

- The Composer
- Slot Table
- Snapshot system
- Recomposition internals
- How Compose knows what changed

→ [`books/book-2-architecture-and-ui/08-inside-compose-runtime/`](./books/book-2-architecture-and-ui/08-inside-compose-runtime/)

---

### DOC 9 — Production Compose Patterns
> *How Experienced Engineers Build Compose Apps*

- State architecture with Compose
- Side effects (LaunchedEffect, SideEffect, DisposableEffect)
- Design systems and theming (MaterialTheme, tokens)
- Performance patterns (key(), derivedStateOf)

→ [`books/book-2-architecture-and-ui/09-production-compose-patterns/`](./books/book-2-architecture-and-ui/09-production-compose-patterns/)

---

### DOC 10 — Design Systems at Scale
> *How Large Teams Organize UI Code*

- Why design systems exist — three teams, three shades of blue, one broken brand
- Design tokens: named values (`colorPrimary`, `spacingMedium`) instead of raw hex/dp
- Two-level token system: raw palette → semantic tokens → MaterialTheme slots
- Component API design — control what callers can configure, enforce what they cannot
- Atomic design, cross-team sharing, design system versioning

→ [`books/book-2-architecture-and-ui/10-design-systems-at-scale/`](./books/book-2-architecture-and-ui/10-design-systems-at-scale/)

---

## Book 3 — Production Systems

**Goal:** Understand how real Android apps handle data at scale.

### DOC 11 — Remote Communication
> *How Apps Talk to Servers*

**Section 1 — Computer Communication Foundations**
- Computer communication basics, packets, protocols

**Section 2 — HTTP**
- HTTP request/response, methods, status codes, headers, body

**Section 3 — APIs & Web Services**
- What an API is, REST, endpoints

**Section 4 — Data Representation**
- Serialization & Deserialization, JSON
- DTOs — [mental model](./books/book-3-production-systems/11-remote-communication/04-data-representation/03-dto/01-mental-model.md) + [production code](./books/book-3-production-systems/11-remote-communication/04-data-representation/03-dto/02-production-code.md)
- API versioning & backward compatibility

**Section 5 — Android Networking**
- [OkHttp](./books/book-3-production-systems/11-remote-communication/05-android-networking/01-okhttp/)
- [Retrofit](./books/book-3-production-systems/11-remote-communication/05-android-networking/02-retrofit/)
- [Converter Factories](./books/book-3-production-systems/11-remote-communication/05-android-networking/03-converter-factories/)
- [Interceptors](./books/book-3-production-systems/11-remote-communication/05-android-networking/04-interceptors/)
- [Timeouts](./books/book-3-production-systems/11-remote-communication/05-android-networking/05-timeouts/)
- [Error Handling](./books/book-3-production-systems/11-remote-communication/05-android-networking/06-error-handling/)

**Section 6 — Production Networking Patterns**
- API Service Layer
- Remote Data Source
- Network Result
- Network Error Modeling

→ [`books/book-3-production-systems/11-remote-communication/`](./books/book-3-production-systems/11-remote-communication/)

---

### DOC 12 — Local Persistence
> *How Apps Store Data Locally*

- Why local persistence exists, the data-disappears-on-close problem
- Room: Entity, DAO, RoomDatabase, TypeConverters
- Reactive queries with Flow — table change → UI update automatically
- Room + Coroutines (suspend DAOs)
- Database migrations, SharedPreferences → DataStore
- Offline-first strategy, cache invalidation

→ [`books/book-3-production-systems/12-local-persistence/`](./books/book-3-production-systems/12-local-persistence/)

---

### DOC 13 — Data Architecture
> *How Data Layers Are Organized*

- Why data architecture exists — the ViewModel-does-too-much problem
- Repository as coordinator: decides when to fetch remote, when to read local
- Single Source of Truth — UI always reads from Room, network writes to Room
- Offline-First Architecture — show cached data immediately, refresh in background
- Domain Model vs DTO vs Entity — three classes, three contracts, two mappers

→ [`books/book-3-production-systems/13-data-architecture/`](./books/book-3-production-systems/13-data-architecture/)

---

### DOC 14 — Dependency Injection
> *How Production Apps Wire Themselves Together*

- Why DI exists — the manual construction cascade problem
- Hilt: `@HiltAndroidApp`, `@AndroidEntryPoint`, `@Inject constructor`
- `@HiltViewModel` — ViewModelProvider lifecycle + Hilt injection combined
- `@Module` / `@Provides` for third-party classes (Retrofit, Room, OkHttp)
- Component hierarchy, scoping (@Singleton, @ViewModelScoped)

→ [`books/book-3-production-systems/14-dependency-injection/`](./books/book-3-production-systems/14-dependency-injection/)

---

### DOC 15 — Background Work
> *How Apps Do Work When Not in Foreground*

- Why coroutines alone fail — tied to ViewModel lifecycle, can't wait for WiFi
- WorkManager: guaranteed execution, survives process death and device reboots
- CoroutineWorker: `suspend fun doWork()` — call any suspend function from a Worker
- Constraints: run only on WiFi, only when charging, only when battery isn't low
- Periodic work, work chaining, retry with exponential backoff

→ [`books/book-3-production-systems/15-background-work/`](./books/book-3-production-systems/15-background-work/)

---

## Book 4 — Mastery & Optimization

**Goal:** Cross-cutting expertise that makes the difference between a competent and a senior engineer.

### DOC 16 — Testing Strategy
> *How to Build Confidence in Production Code*

- Testing pyramid: unit → integration → UI → E2E
- Testing coroutines: `runTest`, `MainDispatcherRule`, `TestDispatcher`
- Fake vs Mock — Fakes catch interface drift; Mocks silently pass
- Testing Room with in-memory database — real SQL, no file I/O
- Testing Compose with `ComposeTestRule` — render, click, assert without a device

→ [`books/book-4-mastery-optimization/16-testing-strategy/`](./books/book-4-mastery-optimization/16-testing-strategy/)

---

### DOC 17 — Performance & Optimization
> *How to Make Systems Fast*

- Profile → Find → Fix loop — never optimize what you haven't measured
- Compose recomposition metrics: compiler report, Layout Inspector recomposition counts
- Baseline Profiles — pre-compile hot classes on install, 20–40% faster cold start
- Memory leaks with LeakCanary — reference chain analysis, DisposableEffect cleanup
- Startup optimization, Perfetto traces, battery-aware background work

→ [`books/book-4-mastery-optimization/17-performance-optimization/`](./books/book-4-mastery-optimization/17-performance-optimization/)

---

### DOC 18 — Android Framework Deep Dive
> *The Platform Beneath Everything*

- Zygote: pre-warmed fork model, why cold start is ~50ms not seconds
- Binder: the IPC backbone behind every `startActivity()` and `getSystemService()`
- Foreground Services: the "run now, keep running" contract backed by a persistent notification
- Services (bound, started), Content Providers, BroadcastReceiver
- Intents & Intent Filters, Android permissions, Doze mode

→ [`books/book-4-mastery-optimization/18-android-framework-deep-dive/`](./books/book-4-mastery-optimization/18-android-framework-deep-dive/)

---

## Book 5 — Learning From Experience

**Goal:** Apply everything by reading real codebases and understanding architectural journeys.

### Case Studies
> Real problems, real solutions — every mental model applied to production code

- [x] [Building Offline-First Order Tracking](./books/book-5-learning-from-experience/01-offline-first-feature.md) — ViewModel → Repository → Room → Flow → UI, the full data pipeline
- [ ] Architecture Journey: From MVP to Clean Architecture
- [x] [Migrating a Screen from XML to Compose](./books/book-5-learning-from-experience/03-migration-xml-to-compose.md) — RecyclerView+Adapter → LazyColumn, incremental migration strategy
- [ ] Production Code Walkthrough: NowInAndroid
- [ ] Case Study: Large-Scale Refactor
- [ ] Building a Feature End-to-End

→ [`books/book-5-learning-from-experience/`](./books/book-5-learning-from-experience/)

---

## Quick Reference

| Book | Core Question Answered |
|------|------------------------|
| Book 1 — Foundations | Why does Android code look like this? |
| Book 2 — Architecture & UI | How is an Android app structured? |
| Book 3 — Production Systems | How does a production app handle real data? |
| Book 4 — Mastery & Optimization | How do senior engineers think about quality? |
| Book 5 — Experience | How do I read and navigate real codebases? |
