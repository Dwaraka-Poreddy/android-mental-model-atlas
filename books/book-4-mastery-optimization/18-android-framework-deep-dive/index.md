# DOC 18 — Android Framework Deep Dive

**Purpose:** Understand the platform beneath everything else

By this point you can build complete production Android applications. This document takes you below the framework level — into how Android itself works: its process model, inter-process communication, system services, and the contracts you must respect to be a good Android citizen.

**Status:** Under Development

## Concepts

### Part 1 — Android OS Architecture
- [x] [App Process and Zygote](./01-app-process-and-zygote.md) — How Android forks processes from Zygote and what happens at app startup
- [ ] ART — Android Runtime — How the Android Runtime compiles and executes bytecode (AOT, JIT, profiles)

### Part 2 — IPC & Binder
- [x] [Binder and IPC](./03-binder-and-ipc.md) — Android's inter-process communication backbone and how system services use it

### Part 3 — Services
- [ ] Services — Bound and Started — The two service types, their lifecycles, and when to use each
- [x] [Foreground Services](./05-foreground-services.md) — Services that show a persistent notification and survive app backgrounding

### Part 4 — Content Providers
- [ ] Content Providers — Exposing structured data across app boundaries using the ContentProvider API

### Part 5 — Intents & Intent Filters
- [ ] BroadcastReceiver — Receiving system-wide events and app-specific broadcasts
- [ ] Intents — Explicit and Implicit — How the Android system routes messages between components

### Part 6 — System Integration
- [ ] Android Permissions — The runtime permission model and how apps request access to protected resources
- [ ] App Startup Library — Optimizing component initialization order at app startup
- [ ] System Integration Patterns — Doze mode, app standby buckets, and battery optimization constraints

## How This Fits Into The Atlas

This document answers: **How does the Android platform work at the OS level?**

This is advanced territory. It explains why certain constraints exist (you can't do X in a background service after Android 8, etc.) and makes you a more informed debugger when something breaks at the platform boundary.

---

**Previous:** [DOC 17 — Performance & Optimization](../17-performance-optimization/index.md)

**Next:** [Book 5 — Learning From Experience](../../book-5-learning-from-experience/index.md)
