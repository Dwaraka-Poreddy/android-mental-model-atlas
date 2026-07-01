# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repository Is

A **dependency-first curriculum** for Android engineering, organized as a sequence of Markdown documents (called the "Atlas"). There is no build system, no tests, and no source code — all content is prose and Kotlin code examples embedded in Markdown files.

## Repository Structure

```
master-index.md          — Canonical reading order and links to all docs
WRITING-GUIDE.md         — The complete spec for how to write Atlas documents
README.md                — Editorial principles and non-negotiable rules
books/
  book-1-foundations/    — Coroutines, Flow, State (DOCs 1–4 + DOC 5 lives here)
  book-2-architecture-and-ui/   — Lifecycle, Compose, Design Systems (DOCs 6–10)
  book-3-production-systems/    — Networking, Persistence, DI, Background (DOCs 11–15)
  book-4-mastery-optimization/  — Testing, Performance, Framework (DOCs 16–18)
  book-5-learning-from-experience/  — Case studies
```

Concepts with both deep mental model content and rich production code patterns are **split** into two files:
- `01-mental-model.md` — Problem, concept, production recognition, revision
- `02-production-code.md` — Patterns, variations, anti-patterns, How To Read It examples

Single-file concepts stay as one doc. Folders are created when 3+ related concept files belong together (max 3 levels of nesting).

## Document Structure (Required Sections)

Every Atlas document must contain these sections in order:

1. **Metadata block** (HTML comment at top): Primary Question, Prerequisites, After Reading, Next Concept
2. **Looking Back** — connects to the immediately preceding concept via the natural question it raised
3. **The Problem** — real-world pain that makes the reader want the solution
4. **[Concept Name]** — the mental model section; one sentence definition + ASCII diagram
5. **Production Code** — 15–25 lines of realistic Kotlin + **How To Read It** (never skip this)
6. **A Natural Question** — the question this concept naturally raises, leading to the next chapter
7. **Revision** — Core Idea, Mental Model diagram, Production Recognition signature, Prev/Next links

Optional sections (add only when they genuinely add understanding): Minimal Code, Production Notes, Common Misconception, Anti-Pattern, Variations, Decision Matrix, Deep Dive, Production Recognition Checklist, Architectural Perspective.

## Core Writing Rules

1. **Problem before API** — never introduce a class before establishing why anyone would want it
2. **One primary question per document** — if you can't summarize in one sentence, split
3. **Mental model before implementation** — the memorable one-sentence insight comes before any code
4. **"How To Read It" is never optional** — it teaches thinking, not syntax; always follows Production Code
5. **"A Natural Question" must feel inevitable** — if the transition feels forced, check the dependency order in master-index.md
6. **Never repeat without adding understanding** — link rather than re-explain

## The Atlas Test

Every document should leave the reader with one memorable sentence of the form:
```
Concept → What It Owns / Does
```
Examples: `ViewModel → Owner Of Screen State`, `Repository → Owner Of Application Data`, `StateFlow → Always Has A Current Value`

## What NOT to Write

- API-first explanations ("StateFlow is a hot Flow that...")
- Boilerplate sections that add no understanding
- Code examples that introduce no new mental model
- Manufactured curiosity closers ("But wait, there's more!")
- Repetition of what was explained in a previous document (link instead)
