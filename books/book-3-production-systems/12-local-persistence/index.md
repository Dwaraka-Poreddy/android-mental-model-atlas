# DOC 12 — Local Persistence

**Purpose:** How Applications Remember Information

This document teaches how Android applications store data locally — primarily with Room, Android's recommended database library. It connects directly to DOC 11 (Remote Communication) by answering: once you fetch data from a server, where does it live on the device?

## Concepts

### Part 1 — Why Persistence Matters
- [x] [Why Local Persistence Exists](./01-why-local-persistence-exists.md) — The problem local storage solves and why apps need it

### Part 2 — Database Foundations
- [ ] Database Fundamentals (tables, rows, queries) — The relational model and how databases organize information
- [ ] SQL Basics (SELECT, INSERT, UPDATE, DELETE) — The query language used to read and write database records

### Part 3 — Room
- [x] [Room — Android's Database Library](./04-room.md) — The ORM that wraps SQLite with type-safety and coroutine support
- [x] [Entity](./05-entity.md) — Kotlin data classes that map to database tables
- [x] [DAO (Data Access Object)](./06-dao.md) — Interfaces that declare the queries for a given table
- [ ] RoomDatabase — The central access point that ties entities and DAOs together
- [ ] TypeConverters — How to store non-primitive types by teaching Room to serialize them

### Part 4 — Room + Reactive Patterns
- [x] [Room + Flow (reactive queries)](./09-room-and-flow.md) — Using Flow to observe database changes automatically
- [ ] Room + Coroutines (suspend DAOs) — Writing suspend functions in DAOs for one-shot reads and writes

### Part 5 — Schema Evolution
- [ ] Database Migrations — How to change the database schema without destroying user data
- [ ] Migration Testing — Verifying that migrations run correctly across schema versions

### Part 6 — Preferences & Key-Value Storage
- [ ] SharedPreferences (legacy) — The original Android key-value store and its limitations
- [ ] DataStore (modern replacement) — Jetpack's asynchronous, type-safe replacement for SharedPreferences
- [ ] Preferences DataStore — Storing untyped key-value pairs using DataStore
- [ ] Proto DataStore — Storing typed objects using Protocol Buffers with DataStore

### Part 7 — Choosing & Scaling
- [ ] When to Use What (Room vs DataStore vs File) — Decision framework for picking the right storage mechanism
- [ ] Offline-First Strategy — Designing apps to work without a network connection by default
- [ ] Cache Invalidation — Knowing when locally stored data is stale and needs refreshing
- [ ] Production Database Patterns — Indexing, query optimization, and patterns used in real production apps

## Reading Order

Concepts should be read in the order listed because:

1. **Understand why** (Why Local Persistence Exists)
2. **Learn the foundation** (Database concepts, SQL)
3. **Learn Room** (Entity, DAO, RoomDatabase — the core API)
4. **Connect to reactive patterns** (Room + Flow, Room + Coroutines — bridges DOC 3 and DOC 2)
5. **Handle evolution** (Migrations — production reality)
6. **Learn alternatives** (DataStore for simple key-value storage)
7. **Make decisions** (When to use what, offline-first patterns)

## How This Fits Into The Atlas

This document answers: **How do Android applications store data locally?**

After learning how to fetch data from servers (DOC 11), this document teaches how to persist that data on device — enabling offline support, caching, and fast local reads.

The next document, **DOC 13 — Data Architecture**, teaches how to combine remote and local data sources into one coherent architecture through the Repository pattern.

---

**Start with:** [Why Local Persistence Exists](./01-why-local-persistence-exists.md)
