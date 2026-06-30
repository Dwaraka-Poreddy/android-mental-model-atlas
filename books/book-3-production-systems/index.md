# Book 3 — Production Systems

This book teaches how real Android applications handle data at scale: fetching from the network, persisting locally, organizing data access, managing dependencies, and running background work.

**Assumes:** Books 1 & 2 (coroutines, flows, state, Android architecture, Compose)

## Documents

- [ ] **[DOC 11 — Remote Communication](./11-remote-communication/)** — HTTP, APIs, OkHttp, Retrofit, production networking patterns *(all sections have starter content; some production code docs unwritten)*
- [ ] **[DOC 12 — Local Persistence](./12-local-persistence/)** — Room, Entity, DAO, Flow queries, migrations, DataStore *(6 of 20 concepts written)*
- [ ] **[DOC 13 — Data Architecture](./13-data-architecture/)** — Repository, Single Source of Truth, Offline-First, Domain Models *(5 of 18 concepts written)*
- [ ] **[DOC 14 — Dependency Injection](./14-dependency-injection/)** — Hilt, @Inject, @HiltViewModel, @Module, @Provides, scoping *(5 of 16 concepts written)*
- [ ] **[DOC 15 — Background Work](./15-background-work/)** — WorkManager, CoroutineWorker, Constraints, periodic work *(4 of 14 concepts written)*

## Reading Order

Follow the order above. Each document builds on the previous.

## Goal

By the end of this book, you should:
- ✅ Know how to fetch data from any server endpoint
- ✅ Know how to cache data locally with Room
- ✅ Understand offline-first architecture
- ✅ Be able to wire a production app with Hilt
- ✅ Know how to schedule reliable background work

---

**After Book 3:** [Book 4 — Mastery & Optimization](../book-4-mastery-optimization/index.md)
