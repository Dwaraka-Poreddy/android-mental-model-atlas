# Android Mental Model Atlas

A first-principles Android knowledge base focused on understanding concepts, mental models, architecture decisions, and the relationships between them.

The goal of this repository is not to collect notes.

The goal is to build a long-term understanding of Android development that remains useful years later.

---

# Vision

Most technical resources focus on:

```text
What
```

and:

```text
How
```

This atlas focuses equally on:

```text
Why
```

Every concept in Android exists because a problem existed first.

Understanding the problem often makes the solution obvious.

The purpose of this repository is to build and preserve those mental models.

The atlas is intended to remain useful for:

- Revising Android concepts
- Rebuilding forgotten knowledge
- Understanding architecture decisions
- Preparing for interviews
- Onboarding to unfamiliar codebases
- Understanding production Android systems

The repository is designed as a knowledge graph rather than a collection of isolated notes.

---

# Why This Repository Exists

Modern Android development contains many interconnected concepts:

- Processes
- Threads
- Coroutines
- Flow
- State Management
- Lifecycle
- ViewModel
- Compose
- Architecture Patterns

These concepts are often taught independently.

As a result, developers frequently learn:

```text
How To Use Something
```

without understanding:

```text
Why It Exists
```

or:

```text
What Problem It Solves
```

This repository exists to close that gap.

For every concept, the atlas attempts to answer:

- What is it?
- Why was it created?
- What problem does it solve?
- What concepts should be understood before it?
- What concepts naturally come after it?
- What misconceptions commonly exist?
- How does it fit into the larger Android ecosystem?

The focus is understanding rather than memorization.

---

# Core Philosophy

Whenever a new concept is introduced, the atlas attempts to answer:

1. What problem existed before this concept?
2. Why was this concept created?
3. What problem does it solve?
4. What concepts should be understood before learning it?
5. What concepts naturally come after it?

The preferred learning path is:

```text
Problem
↓
Concept
↓
API
```

Never:

```text
API
↓
Concept
↓
Problem
```

Understanding the problem is more important than memorizing the solution.

---

# What Makes This Atlas Different?

The atlas is not intended to be:

```text
A Cheat Sheet
```

---

It is not intended to be:

```text
A Collection Of Interview Notes
```

---

It is not intended to be:

```text
A List Of APIs
```

---

Instead, it attempts to build:

```text
Durable Mental Models
```

that remain useful even when APIs evolve.

The repository prioritizes:

```text
Understanding
```

over:

```text
Memorization
```

and:

```text
Reasoning
```

over:

```text
Recall
```

---

# How Documents Are Written

The Atlas follows a strict documentation methodology.

Many of these principles were discovered and refined while writing the first 60 concepts of the repository.

---

## Problem Before Concept Before API

The preferred teaching order is:

```text
Problem
↓
Concept
↓
API
```

A reader should first understand:

```text
Why Something Exists
```

before learning:

```text
How To Use It
```

For example:

```text
Who Owns This Coroutine?
↓
CoroutineScope
```

is preferred over:

```text
CoroutineScope Definition
↓
Usage Examples
```

Understanding the problem creates a reason for the solution to exist.

---

## One Chapter = One Idea

Every chapter should teach one primary idea.

If a chapter starts answering multiple major questions, it should usually be split.

Examples:

```text
Exception Handling
```

and:

```text
Exception Propagation
```

are separate concepts and therefore separate chapters.

The goal is to reduce cognitive load and improve clarity.

---

## Questions Must Be Earned

The Atlas avoids introducing questions that a reader is unlikely to have naturally.

Bad example:

```text
launch And async Behave Differently.

Why?
```

The reader may not yet know they behave differently.

Preferred approach:

```text
launch Returns Job

async Returns Deferred

Do Failures Behave Differently?
```

The question emerges naturally from existing knowledge.

Questions should feel discovered rather than injected.

---

## Acknowledge Questions Before Deferring Them

Sometimes a concept naturally creates questions that belong to future chapters.

In these situations the Atlas follows:

```text
Acknowledge
↓
Defer
```

rather than:

```text
Ignore
```

Readers should feel that the gap is intentional.

Not forgotten.

---

## Repetition Is Not Explanation

A concept should not be restated unless new information is being introduced.

The Atlas continuously asks:

```text
Does This Section Introduce New Understanding?
```

If the answer is:

```text
No
```

the section should usually be removed.

The goal is:

```text
Maximum Clarity With Minimum Words
```

---

## The Reader Is Assumed To Be Intelligent

The Atlas avoids:

- Artificial suspense
- Manufactured questions
- Unnecessary repetition

The documentation should answer genuine questions rather than attempting to create them artificially.

---

## Mental Models Are Prioritized

Mental models often provide more long-term value than API descriptions.

Examples:

```text
Family Relationships
→ Parent Child Hierarchy

House Construction
→ Structured Concurrency

Firefighter vs Incident Reporter
→ try-catch vs CoroutineExceptionHandler
```

Mental models exist to improve reasoning and retention.

They are not decorative analogies.

---

## Explicit Abstraction Levels

Many concepts can be explained at multiple levels.

Examples:

```text
Conceptual

Behavioral

Implementation
```

Every chapter should make its abstraction level clear.

The Atlas generally teaches:

```text
Conceptual Understanding
```

before implementation details.

This prevents unnecessary confusion.

---

## Every Section Must Earn Its Place

Before adding a section, the following question is asked:

```text
If This Section Is Removed Does Understanding Decrease?
```

If the answer is:

```text
No
```

the section should usually be removed.

This helps prevent documentation bloat.

---

## Don't Reopen Solved Problems

Once a concept solves a problem:

```text
CoroutineScope
↓
Ownership
```

later chapters should build on that solution rather than repeatedly reintroducing the same problem.

The atlas attempts to move forward through the dependency graph of concepts.

---

## Use Existing Knowledge To Build New Knowledge

The strongest conceptual bridges are built using ideas the reader already understands.

Bad bridge:

```text
launch And async Handle Exceptions Differently
```

Good bridge:

```text
launch Returns Job

async Returns Deferred

Do Failures Behave Differently?
```

The second bridge uses existing knowledge to create curiosity.

---

# Repository Structure

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

---

## Default Rule

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

Supporting concepts may be grouped when they do not justify separate files.

The structure exists to support learning rather than enforce arbitrary organization.

---

# How To Navigate

The recommended reading order follows concept dependencies rather than alphabetical ordering.

Start here:

👉 **[Master Index](./00-master-index/master-index.md)**

The Master Index contains:

- Reading order
- Progress tracking
- Atlas structure
- Current roadmap
- Future expansion plans

The Master Index acts as the source of truth for the repository.

---

# Current Scope

The atlas currently covers or plans to cover:

- Computer & Android Foundations
- Coroutines Core
- Reactive Streams & Flow
- State Management
- Android Lifecycle & Architecture
- Compose Foundations
- Advanced Compose
- Production Android Architecture

Future topics may include:

- Navigation
- Dependency Injection
- Hilt
- Room
- WorkManager
- Testing
- Offline First Architecture
- Modularization
- Performance Optimization

The repository is intentionally expandable.

---

# Lessons Learned While Building The Atlas

The Atlas itself evolved significantly while being written.

Many of the current rules were discovered through mistakes and later refinements.

Some notable lessons:

- A chapter is often too large before it becomes too small.
- Questions should emerge naturally from existing knowledge.
- Repetition frequently disguises itself as explanation.
- The best conceptual bridges use information the reader already knows.
- Missing concepts often reveal themselves only after surrounding concepts are written.
- Some sections deserve promotion into standalone chapters.
- Mental models often provide more long-term value than API descriptions.
- Documentation structure should evolve when understanding evolves.

The Atlas is therefore not only a knowledge base about Android.

It is also an evolving experiment in how technical knowledge can be taught more effectively.

---

# Philosophy Of Change

This repository is a living document.

Concepts may be:

- Expanded
- Refined
- Reorganized
- Split into multiple chapters
- Merged into larger concepts

as understanding grows.

The structure exists to serve learning.

Learning does not exist to serve the structure.

---

# Start Here

➡️ **[Open the Master Index](./00-master-index/master-index.md)**

The Master Index is the entry point and source of truth for the entire Android Mental Model Atlas.
