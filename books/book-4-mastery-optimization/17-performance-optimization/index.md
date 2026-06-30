# DOC 17 — Performance & Optimization

**Purpose:** Make systems fast and efficient

This document teaches performance profiling, measurement, and optimization techniques.

## Concepts

### Part 1 — Measuring First
- [x] [Why Performance Matters](./01-why-performance-matters.md) — The user impact of performance problems and why measurement comes before optimization
- [ ] Measuring Before Optimizing — Why guessing is wrong and how to identify real bottlenecks
- [ ] Android Profiler — Using Android Studio's CPU, memory, and network profiler
- [ ] Perfetto — System-level tracing for diagnosing complex performance issues

### Part 2 — Compose Performance
- [x] [Compose Recomposition Metrics](./05-compose-recomposition-metrics.md) — Using compiler metrics to detect excessive recomposition
- [ ] Compose Performance (stable types, skipping) — Making composables skippable by ensuring parameter stability
- [ ] Layout Inspector for Compose — Inspecting the live composition tree to find layout issues

### Part 3 — App Startup
- [x] [Baseline Profiles](./08-baseline-profiles.md) — Pre-compiling hot code paths to improve cold start time
- [ ] Startup Optimization (cold/warm/hot) — Understanding the three startup modes and how to improve each

### Part 4 — Memory
- [x] [Memory Leaks (LeakCanary)](./10-memory-leaks.md) — Detecting and fixing memory leaks using LeakCanary

### Part 5 — Layer-Specific Optimization
- [ ] Coroutine Performance (dispatcher choice, blocking) — Avoiding dispatcher misuse and blocking calls on the main thread
- [ ] Database Performance (indexes, query optimization) — Writing efficient Room queries and using indexes correctly
- [ ] Network Performance (caching, compression) — Using HTTP caching headers and compression to reduce latency
- [ ] Battery & Thermal Optimization — Reducing unnecessary wake locks, polling, and CPU-intensive background work

### Part 6 — Build & Delivery
- [ ] R8 & Code Shrinking — Using R8 to shrink, obfuscate, and optimize the APK
- [ ] Production Performance Monitoring — Using Firebase Performance or similar to track real-user metrics in production

## Reading Order

Concepts should be read in the order listed because:

1. **Always measure first** (Why Performance Matters, Measuring Before Optimizing, profiling tools)
2. **Fix Compose** (recomposition metrics, stability, layout inspector)
3. **Fix startup** (Baseline Profiles, startup optimization)
4. **Fix memory** (LeakCanary, leak patterns)
5. **Fix layer-specific issues** (coroutines, database, network, battery)
6. **Optimize delivery** (R8, production monitoring)

## How This Fits Into The Atlas

This document answers: **How do I make my systems fast and efficient?**

After learning to test code (DOC 16), readers learn to optimize it.

The next document, **DOC 18 — Android Framework Deep Dive**, teaches platform internals.

---

**Start with:** [Why Performance Matters](./01-why-performance-matters.md)
