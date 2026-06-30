# Section: Case Studies

Real production problems and solutions from actual Android applications.

Each case study is self-contained and can be read independently. They show:
- **The Situation** — Team size, app maturity, constraints
- **The Problem** — What they struggled with
- **The Solutions Considered** — Trade-offs between approaches
- **The Chosen Approach** — What they implemented and why
- **Lessons Learned** — What they'd do differently next time

## Case Studies

- [x] [Offline-First Order Tracking](../01-offline-first-feature.md) — ViewModel → Repository → Room → Flow → UI, the full data pipeline
- [x] [Migration from XML to Compose](../03-migration-xml-to-compose.md) — RecyclerView+Adapter → LazyColumn, incremental migration strategy
- [ ] Performance Optimization Journey — Profiling, Baseline Profiles, recomposition fixes, LeakCanary
- [ ] Monolith to Modular Architecture Migration — Feature modules, API boundaries, navigation across modules
- [ ] Scaling Teams and Code Organization — Multi-module, design systems, shared infrastructure
- [ ] Handling Legacy Code Integration — Wrapping old code, incremental rewrites, interop patterns
