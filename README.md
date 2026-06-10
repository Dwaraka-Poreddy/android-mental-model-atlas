# Android Mental Model Atlas

A first-principles Android knowledge base focused on understanding concepts, mental models, architecture decisions, and the relationships between them.

The goal of this repository is not to collect notes, but to build a long-term understanding of Android development that remains useful years later.

---

## Why This Repository Exists

Modern Android development contains many interconnected concepts:

- Processes
- Threads
- Coroutines
- Flow
- State Management
- Android Lifecycle
- ViewModel
- Jetpack Compose
- Architecture Patterns

Learning these concepts individually often leads to memorization without understanding.

This repository aims to document:

- What each concept is
- Why it exists
- What problem it solves
- How it relates to other concepts
- Common misconceptions
- Real-world usage patterns

The focus is on building mental models rather than memorizing APIs.

---

## Guiding Principles

Whenever a new concept is introduced, the goal is to answer:

1. What problem existed before this concept?
2. Why was this concept created?
3. What problem does it solve?
4. What concepts should be understood before learning it?
5. What concepts naturally come after it?

Understanding the problem is more important than memorizing the solution.

---

## Repository Structure

The atlas is organized into documents (folders) and concepts (files).

```text
.
├── README.md
├── 00-master-index/
├── 01-foundations/
├── 02-coroutines-core/
├── 03-flow/
├── 04-state-management/
├── 05-android-architecture/
├── 06-compose-foundations/
├── 07-advanced-compose/
└── 08-production-android/
```

### Default Rule

```text
One Major Concept = One File
```

Examples:

```text
process.md
thread.md
coroutine.md
flow.md
stateflow.md
viewmodel.md
remember.md
```

Supporting concepts may be grouped together when they do not justify separate files.

---

## How To Navigate

The recommended reading order follows the dependency chain of concepts.

Start here:

👉 **[Master Index](./00-master-index/master-index.md)**

The Master Index contains:

- Complete roadmap
- Reading order
- Progress tracking
- Future expansion plans
- Atlas structure

---

## Current Scope

The atlas currently covers or plans to cover:

- Computer & Android Foundations
- Coroutines
- Reactive Streams & Flow
- State Management
- Android Lifecycle & Architecture
- Jetpack Compose
- Advanced Compose
- Production Android Architecture

Future topics may include:

- Navigation
- Dependency Injection
- Hilt
- Room
- WorkManager
- Testing
- Offline-First Architecture
- Modularization
- Performance Optimization

---

## Philosophy

This repository is a living document.

Concepts may be:

- Expanded
- Reorganized
- Split into separate documents
- Refined as understanding grows

The structure exists to serve learning, not the other way around.

---

## Start Here

➡️ **[Open the Master Index](./00-master-index/master-index.md)**

The Master Index is the source of truth for the entire atlas.
