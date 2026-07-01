# Android Mental Model Atlas — Writer's Guide

---

## The 6 Rules

Every document you write should honor these, in order.

**Rule 1: One primary question per document.**
A document that answers two questions answers neither well. Before writing, state the single question this document exists to answer. If you can't do it in one sentence, split the document.

**Rule 2: Problem before solution.**
No reader wants to learn an API. Readers want to solve problems. Show the pain — the bug, the coupling, the consequence — before introducing the concept. Make them want it.

**Rule 3: Mental model before implementation.**
The mental model is the point. The code is evidence. Present the core insight first — the one sentence a reader will remember in a month — before any code appears.

**Rule 4: Production code teaches recognition, not syntax.**
Every code example should teach the reader how to *think* about the code, not just what the APIs do. "How To Read It" is not a box to check — it is the difference between teaching syntax and building understanding.

**Rule 5: End with the natural question.**
The best endings make the next chapter feel inevitable. The "A Natural Question" closer should arise directly from this document's concept — not be invented for effect. If the curiosity isn't genuine, the dependency chain is probably wrong.

**Rule 6: Never repeat without adding understanding.**
If you've explained something once, a link is better than a re-explanation. If new context genuinely changes the understanding, explain it. If it doesn't, cut it.

**Rule 7: Every section must earn its place.**
A longer document is not a better document. Every section should introduce a new idea, deepen understanding, or improve recognition. If removing a section doesn't reduce the reader's understanding, remove it. Clarity comes from relevance, not length.

---

## Before You Write: Metadata Block

Every document starts with a metadata block. This is your contract with the reader — and a forcing function for clarity before the first word.

```markdown
<!--
Primary Question: [The one question this document answers]
Prerequisites: [DOC N — Concept Name], [DOC N — Concept Name]
After Reading: [What the reader will be able to do or recognize]
Next Concept: [Concept Name]
-->
```

**Example:**
```markdown
<!--
Primary Question: Who should own screen state when an Activity can be recreated?
Prerequisites: DOC 5 — Configuration Changes
After Reading: You can read any ViewModel and understand what it owns and why
Next Concept: ViewModelStore
-->
```

If you cannot fill in "Primary Question" without hedging, stop. The document is not ready to write.

---

## Quick Start

Before you write a single line, answer these four questions:

1. **What is the ONE primary question this document answers?** (If more than one, split)
2. **What question from the previous concept does this answer?** (Required for "Looking Back")
3. **What concrete real-world problem does this concept solve?** (Required for "The Problem")
4. **Can I summarize the answer in one sentence?** (Required for the Mental Model)

If all four have clear answers, start writing.

---

## Document Structure

Sections marked **[REQUIRED]** must appear in every document. Sections marked **[OPTIONAL]** are tools — use them when they genuinely add understanding, skip them when they don't.

---

### [REQUIRED] Title and Metadata

```markdown
<!--
Primary Question: ...
Prerequisites: ...
After Reading: ...
Next Concept: ...
-->

# Concept Name
```

**Rules:**
- Use the exact concept name from the master-index
- Keep it short (1-3 words ideal)
- Specific, not generic ("ViewModel" not "Screen State Owner")

---

### [REQUIRED] Looking Back

```markdown
## Looking Back

In [previous concept], we learned:

```text
[Core idea from that concept]
```

A natural question appears:

```text
[The question this document answers]
```
```

**Purpose:** Create explicit dependency awareness. The reader understands why they are learning this now — not later, not earlier.

**Rules:**
- Always reference the immediately previous concept
- State the exact question this document exists to answer
- The question should feel earned, not forced
- Use the `text` block formatting for visual clarity

**Example:**
```
## Looking Back

In DOC 5 — ViewModel, we learned:

```text
ViewModel
↓
The Owner Of Screen State
```

A natural question appears:

```text
If The Activity Is Destroyed,
How Does Android Reconnect The New Activity
With The Existing ViewModel?
```
```

**Length:** 5-10 lines.

---

### [REQUIRED] The Problem

**Purpose:** Readers understand WHY this concept exists before seeing the API. Make them want the solution.

**Rules:**
- Start with a real problem, not an API
- Use relatable domains: food delivery, e-commerce, social media
- Concrete business examples work better than abstract ones
- Use real names (Dwaraka, Priya, Alice) when helpful
- Show the pain point clearly — the bug, the crash, the coupling

**Three valid problem formats:**

```
# Thought experiment:
"Imagine a company that regularly receives packages from different suppliers.
Each supplier labels and packages items in its own way..."

# Concrete business scenario:
"Suppose the user changes their name.
Now: HomeViewModel? ProfileViewModel? CartViewModel?
Which copy should be updated?"

# Real user action:
"User opens Search → Types 'Chicken Biryani' → Rotates Device.
Without a solution, the typed text disappears."
```

**Length:** 15-30 lines.

---

### [REQUIRED] The Mental Model

**Purpose:** The core insight. One sentence the reader will still remember in six months.

**Rules:**
- One sentence that captures the essence
- Solve the exact problem stated above
- Include at least one ASCII diagram
- Move from concrete → abstract progressively

**Template:**
```markdown
## [Concept Name]

A [Concept] represents:

```text
[One sentence definition]
```

[Why this is the right mental model — connect it to the problem]

[ASCII diagram showing the structure or relationship]
```

**ASCII diagram pattern:**
```
Before:
├─ Activity → State (mixed responsibility)

After:
├─ Activity → Displays
└─ ViewModel → Owns State

Why: Each object has one responsibility
```

**Example from Repository document:**
```
## Repository

A Repository represents:

```text
The Owner Of Application Data
```

Instead of:
HomeViewModel → User
ProfileViewModel → User
CartViewModel → User

we have:
UserRepository → User
  ↓
HomeViewModel, ProfileViewModel, CartViewModel

Now: One Owner, Many Readers
```

**Length:** 50-100 lines. Deep enough to understand, not so deep it loses clarity.

---

### [REQUIRED] Production Code

**Purpose:** Teach readers how to recognize and interpret this pattern in real codebases.

**Rules:**
- Use code that could exist in a real codebase — real variable names, not Foo/Bar
- 15-25 lines of code (realistic, not stripped-down)
- ALWAYS follow with "How To Read It" — this section is never optional here
- "How To Read It" teaches the mental model, not the syntax
- Focus readers on responsibilities and ownership, not method calls

**Template:**
```markdown
## Production Code

```kotlin
[Realistic code]
```

### How To Read It

Don't read it as:
```text
[Surface-level reading — API names and what each line does]
```

Read it as:
```text
[Mental model reading — responsibilities, ownership, flow of data]
```

Focus on: [The one thing that matters most]
```

**Example:**
```markdown
## Production Code

```kotlin
class HomeViewModel(
    private val repository: UserRepository
) : ViewModel() {

    val user: StateFlow<User?> =
        repository.observeCurrentUser()
            .stateIn(viewModelScope, SharingStarted.Eagerly, null)

    init {
        viewModelScope.launch { repository.refreshCurrentUser() }
    }
}
```

### How To Read It

Don't read it as:
```text
HomeViewModel
↓
Calls repository.observeCurrentUser()
↓
Converts to StateFlow with stateIn()
↓
Launches refresh in init
```

Read it as:
```text
HomeViewModel
↓
Requests Application Data (doesn't own it)
↓
Repository Owns That Data (single source of truth)
↓
ViewModel Builds Screen State From What Repository Provides
↓
Exposes Read-Only State To The UI
```

Focus on: The flow of responsibility, not the method calls.
```

**Length:** 20-50 lines (code + explanation).

---

### [REQUIRED] A Natural Question

**Purpose:** Create genuine curiosity. The reader should want to read the next chapter.

**Rules:**
- The question must feel inevitable — it should arise from this concept naturally
- Never manufacture curiosity ("But wait, there's more!")
- Link to the next concept by name
- 3-5 lines only

**Template:**
```markdown
## A Natural Question

[The question that naturally arises from understanding this concept]

That question leads us to the next chapter:

```text
[Next Concept Name]
```
```

**Example:**
```
## A Natural Question

If the Activity is destroyed, how does Android reconnect
the new Activity with the existing ViewModel?

That question leads us to the next chapter:

```text
ViewModelStore
```
```

---

### [REQUIRED] Revision

**Purpose:** A summary readers can return to for review — without re-reading the entire document.

**Template:**
```markdown
## Revision

### Core Idea

```text
[One sentence — the mental model in its final form]
```

### Mental Model

```text
[ASCII diagram of the structure or flow]
```

### Production Recognition

```kotlin
[The minimal code signature that identifies this pattern]
```

```text
[What to look for when scanning an unfamiliar codebase]
```

### Previous Concept

[Previous concept name]

### Next Concept

[Next concept name]
```

**Example:**
```
## Revision

### Core Idea

```text
Repository = The Coordinator That Owns A Domain's Data
```

### Mental Model

```text
ViewModel
↓
Repository (coordinator — decides: network or cache?)
↓              ↓
RemoteDataSource   DAO
(network)          (database)
```

### Production Recognition

```kotlin
class UserRepository(
    private val remoteDataSource: UserRemoteDataSource,
    private val userDao: UserDao
) {
    fun observeCurrentUser(): Flow<User?> = ...
    suspend fun refreshCurrentUser() = ...
}
```

```text
Two dependencies. Two responsibilities. One coordinator.
```

### Previous Concept

Local Data Source

### Next Concept

Single Source of Truth
```

**Length:** 20-30 lines.

---

## Optional Tools

Use these when they genuinely add understanding. Do not add them because the template has them. If you can remove a section without reducing the reader's understanding, remove it.

---

### [OPTIONAL] Minimal Code

**When to add:** When the concept needs to be seen stripped-down before production complexity is introduced. Useful when the production code example would confuse before the concept is clear.

**Rules:**
- Absolute minimum code — 3-8 lines
- No imports, no boilerplate, just the core structure
- Always followed later by the fuller Production Code section

**Example:**
```kotlin
// Minimal Code — shows the idea, not the full pattern
class SearchViewModel : ViewModel()
```

**Not:**
```kotlin
// This belongs in Production Code, not Minimal Code
@HiltViewModel
class SearchViewModel @Inject constructor(
    private val repository: UserRepository
) : ViewModel() { ... }
```

---

### [OPTIONAL] Production Notes

**When to add:** When there are 3-5 memorable facts that experienced engineers carry without thinking — things that don't fit naturally into prose but readers will need to recognize.

**Rules:**
- 4-6 bullets, each a complete thought
- Include at least one "avoid" or "don't"
- Facts, not explanations

**Example:**
```text
• ViewModels own screen state. Activities and Fragments display it.

• Most production ViewModels expose immutable StateFlow while keeping MutableStateFlow private.

• A ViewModel's scope is tied to a logical screen, not an Activity instance.

• Don't put business logic in a ViewModel — that belongs in the domain layer.
```

---

### [OPTIONAL] How Did This Evolve?

**When to add:** When the concept has a recognizable historical progression — a sequence of real design decisions that led to the current pattern. Not every concept has a meaningful evolution worth showing. Add it when the journey itself teaches something the destination alone doesn't.

Stories are memorable. A reader who understands *why* each stage was inadequate will remember the final pattern far longer than a reader who only sees the end state.

**Rules:**
- Show the evolution as a vertical progression, not a table
- Each stage must be a genuine improvement — not just a rename
- Label each stage with what problem it introduced or solved
- Keep it tight — 3-5 stages maximum

**Template:**
```markdown
## How Did This Evolve?

```text
V1 — [What teams did first]
↓ Problem: [Why it broke down]

V2 — [The first fix]
↓ Problem: [Why this still wasn't enough]

V3 — [The mature pattern]
```
```

**Example:**
```markdown
## How Did This Evolve?

```text
V1 — Throw exceptions from network calls
↓ Problem: Catch blocks scattered across every ViewModel

V2 — Catch at the call site, return null on failure
↓ Problem: Callers can't distinguish "no data" from "error"

V3 — Exception Mapper at the boundary
↓ Problem: Error type still varies by caller — no shared contract

V4 — NetworkResult<T>
sealed class with Success, Error, Loading
One contract. Every caller reads it the same way.
```
```

**Length:** 10-20 lines.

---

### [OPTIONAL] Common Misconception

**When to add:** When there is a widespread wrong mental model that needs to be explicitly named and corrected. Not every concept has a misconception worth naming — only add this when the wrong idea is genuinely common.

**Template:**
```markdown
## Common Misconception

A common misconception is:

```text
[The wrong idea, stated clearly]
```

Incorrect.

```text
[The correct idea]
```

[Why the distinction matters in production]
```

**Example:**
```
## Common Misconception

A common misconception is:

```text
ViewModel = The Screen
```

Incorrect.

```text
Activity / Fragment = Displays The Screen
ViewModel = Owns The Screen State
```

The UI and its state have different responsibilities.
A ViewModel can outlive the Activity that displays it.
Conflating the two leads to state management bugs on configuration change.
```

---

### [OPTIONAL] Anti-Pattern

**When to add:** When there is a common wrong approach that causes real production bugs. The pattern must be genuinely dangerous or widespread — not just aesthetically wrong.

**Template:**
```markdown
## Anti-Pattern: [What People Do Wrong]

```kotlin
[Bad code example]
```

### Why It Breaks

- [Consequence 1]
- [Consequence 2]

### How To Fix

```kotlin
[Good code example]
```
```

---

### [OPTIONAL] Variations

**When to add:** When readers will encounter multiple valid implementations in production and need to distinguish them. Not every concept has meaningful variations — only add this when the differences matter.

**Template:**
```markdown
## Variations You'll See In Production

### Standard Pattern (Google Recommended)
```kotlin
[Code]
```
Why: [Reasoning]

### Older Approach (Still Common)
```kotlin
[Code]
```
Why: [Was standard 2-3 years ago — where you'll still find this]

### Quick-and-Dirty (Small Teams)
```kotlin
[Code]
```
Why: [Speed over structure — when this is intentional]
```

---

### [OPTIONAL] Decision Matrix

**When to add:** When the concept has competing patterns and readers need a framework for choosing between them.

**Example:**
```markdown
## When Should I Use This?

| Situation | Use | Why | Trade-off |
|-----------|-----|-----|-----------|
| Screen state that must survive config change | StateFlow | Always has current value | More boilerplate |
| Real-time notifications, no history needed | SharedFlow | No initial value | Subscribers miss events if not active |
| Pub-sub events across the app | Channel | Backpressure support | Most complex |
```

---

### [OPTIONAL] Deep Dive

**When to add:** When there is important complexity that would interrupt the main flow but readers need for production work. Should be skippable on first read.

**Rules:**
- Must be genuinely important, not nice-to-know
- Don't repeat content from the main flow
- Explain nuances and edge cases, not basics

---

### [OPTIONAL] Production Recognition Checklist

**When to add:** When the pattern is distinctive enough to identify in an unfamiliar codebase, and there are plausible false positives worth addressing.

**Example:**
```markdown
## Production Recognition Checklist

When you open a real Android codebase, you can spot this pattern in ~30 seconds:

□ Class is named `*Repository` or ends in `Repository`
□ Constructor accepts both a local and a remote data source
□ Methods return Domain Models (User, Order), not DTOs or Entities
□ ViewModel injects it — Activities do not

**False Positive to Avoid:**
Manager classes (UserManager, OrderManager) look similar but often mix business logic
and don't own data across features.
```

---

### [OPTIONAL] Architectural Perspective

**When to add:** When the concept affects broader system design and showing how it fits into the larger picture adds meaningful understanding.

**Example:**
```
## Architectural Perspective

Throughout the Atlas we consistently separate responsibilities:

```text
ViewModel = Owns Screen State
Repository = Owns Application Data
```

This separation enables:
- Testing (mock each layer independently)
- Reusability (multiple screens share one repository)
- Maintainability (changes in one layer don't cascade)
```

---

## Writing Style

### Sentence Structure

Use short sentences. Short sentences are clear. Clarity matters.

```
✅ Good
A ViewModel owns screen state.
An Activity displays that state.
They have different responsibilities.

❌ Bad
ViewModels, which are responsible for owning screen state, are distinct from
Activities, which are responsible for displaying that state, a separation that
enables cleaner architecture.
```

### Key Ideas in Isolation

Break core insights out of prose and onto their own line:

```
✅ Good
A Repository represents:

```text
The Owner Of Application Data
```

Why this matters:
Multiple screens can safely share the same repository.

❌ Bad
A Repository is the owner of application data, which is important because
multiple screens need to access the same data.
```

### Concept Before Term

The reader should understand the idea before they see the label:

```
✅ Good
A class that translates JSON responses into Kotlin objects is called a DTO.

❌ Bad
A DTO (Data Transfer Object) translates JSON responses into Kotlin objects.
```

### Code Examples: Real But Readable

```
✅ Good — Real but not overwhelming
class UserViewModel(
    private val repository: UserRepository
) : ViewModel() {
    val user = MutableStateFlow<User?>(null)
}

❌ Bad — Too much noise to see the concept
class UserViewModel @Inject constructor(
    private val repository: UserRepository,
    private val dispatcher: CoroutineDispatcher,
    @Named("local") private val localSource: LocalDataSource,
    @Named("remote") private val remoteSource: RemoteDataSource,
    private val logger: Logger
) : ViewModel() { ... }
```

### ASCII Diagrams

When you are about to write a sentence describing a relationship, draw it instead:

```
✅ Do this
Activity
↓
Displays

ViewModel
↓
Owns State

❌ Not this
A ViewModel is responsible for owning the state of a screen,
while an Activity is responsible for displaying that state...
```

Use liberally. A diagram that takes 5 lines replaces a paragraph.

---

## Decision Trees

### Decision Tree 1: Should This Be A Separate Document?

```
Can I summarize this concept in one sentence?
├─ NO  → Split into smaller concepts
└─ YES → Does this sentence answer a different question than the previous doc?
    ├─ NO  → Fold it in as a section of the adjacent document
    └─ YES → Does it need more than 50 lines to explain fully?
        ├─ NO  → Consider keeping it as a section
        └─ YES → Create a new document
```

Examples:
- `ViewModel` → separate doc (one big idea)
- `ViewModel Lifecycle` → separate doc (different question: when is it created/destroyed?)
- `SavedStateHandle` → separate doc (critical enough for its own focus)
- But `ViewModel + its basic StateFlow usage` → keep together (same concept)

---

### Decision Tree 2: Should This Be Split Into mental-model + production-code?

```
Is this a Book 3+ concept?
├─ NO  (Books 1-2) → Always keep as a single document
└─ YES → Does it have both:
    ├─ Substantial conceptual depth (200+ lines of mental model content)?
    └─ Rich, varied production patterns (200+ lines of code examples)?
        ├─ YES → Split into 01-mental-model.md + 02-production-code.md
        └─ NO  → Single document

01-mental-model.md contains: Problem, Mental Model, Production Recognition, Revision, Natural Question
02-production-code.md contains: Standard Pattern + Example, How To Read It, Stages of Evolution, Common Variations, Anti-Pattern
```

Examples:
- DTO → split (both mental model and code variations matter significantly)
- Repository → split (evolved stages and multiple coordination strategies)
- Room Entity → single doc (concept is simple, code is straightforward)

---

### Decision Tree 3: When to Add Optional Sections

```
Does the concept have multiple valid real-world implementations?
├─ YES → Add Variations or Decision Matrix
└─ NO  → Continue

Did this concept go through recognizable design stages that explain why it looks the way it does?
├─ YES → Add How Did This Evolve?
└─ NO  → Continue

Is there a common dangerous wrong approach?
├─ YES → Add Anti-Pattern
└─ NO  → Continue

Is there important complexity that would interrupt the main flow?
├─ YES → Add Deep Dive
└─ NO  → Continue

Is the pattern distinctive and easy to misidentify?
├─ YES → Add Production Recognition Checklist
└─ NO  → Continue

Does the concept change how you think about the overall architecture?
├─ YES → Add Architectural Perspective
└─ NO  → Done — you have the right sections
```

---

### Decision Tree 4: Should This Concept Have Its Own Folder?

```
Are there 3+ closely related concept files for this topic?
├─ YES → Create a folder
│   (e.g., 04-data-representation/ for DTO, Serialization, JSON, API Versioning)
└─ NO  → Keep in the parent folder as a single file

Maximum nesting depth: 3 levels.

books/book-3-production-systems/
  11-remote-communication/              [L1]
    04-data-representation/             [L2]
      03-dto/                           [L3]
        01-mental-model.md
        02-production-code.md

Anything deeper becomes hard to navigate.
```

---

## Full Annotated Example

Here is a complete document with every section labeled. Use this as your reference when writing a new doc from scratch.

```markdown
<!--                                          ← Metadata block (fill before writing)
Primary Question: Who should own screen state when an Activity can be recreated?
Prerequisites: DOC 5 — Configuration Changes
After Reading: You can read any ViewModel and understand what it owns and why
Next Concept: ViewModelStore
-->

# ViewModel                                   ← [REQUIRED] Title — exact name from master-index

## Looking Back                               ← [REQUIRED] Connect to prerequisites

In DOC 5 — Configuration Changes, we learned:

```text
Activity
↓
Configuration Change
↓
Activity Recreated
```

A natural question appears:

```text
If Activities Can Be Recreated,
Who Should Own The Screen State?
```

---

## The Problem                                ← [REQUIRED] Real pain before the solution

Suppose the user:

```text
Open Search
↓
Type "Chicken Biryani"
↓
Rotate Device
```

The Activity is destroyed and recreated.

Without a solution:

```text
New Activity Created
↓
Search = ""   ← The text is gone
```

The user's input disappeared.

---

## ViewModel                                  ← [REQUIRED] Mental Model — the core insight

A ViewModel represents:

```text
The Owner Of Screen State
```

Instead of storing state inside the Activity:

```text
Activity
↓
Search = "Chicken Biryani"   ← mixed responsibility
```

we move ownership:

```text
Activity          ViewModel
↓                 ↓
Displays    ←     Owns State
```

Now the Activity is responsible for displaying the screen.
The ViewModel is responsible for owning its state.

Each object has a single responsibility.

When the Activity is destroyed and recreated:

```text
Old Activity
↓
ViewModel              ← survives
↓
Search = "Chicken Biryani"   ← unchanged
↓
New Activity
↓
Reconnects to same ViewModel
↓
Search = "Chicken Biryani"   ← still there
```

---

## Minimal Code                               ← [OPTIONAL] Only when the concept needs stripping down

```kotlin
class SearchViewModel : ViewModel()
```

This declares a ViewModel responsible for one screen.

---

## Production Code                            ← [REQUIRED] Realistic code + How To Read It

```kotlin
class SearchViewModel : ViewModel() {

    private val _query = MutableStateFlow("")
    val query = _query.asStateFlow()

    fun onQueryChange(text: String) {
        _query.value = text
    }
}
```

### How To Read It

Don't read it as:
```text
SearchViewModel
↓
MutableStateFlow("")
↓
asStateFlow()
↓
Updates _query.value
```

Read it as:
```text
SearchViewModel
↓
Owns Screen State (the search query)
---
MutableStateFlow
↓
Mutable State — only the ViewModel writes it
---
asStateFlow()
↓
Read-Only State — UI can only observe, not mutate
```

Focus on: Ownership and access control, not the API names.

---

## How Did This Evolve?                       ← [OPTIONAL] Historical progression — only when the journey teaches more than the destination

## Production Notes                           ← [OPTIONAL] Memorable facts for experienced engineers

```text
• ViewModels own screen state. Activities and Fragments display it.

• Expose immutable StateFlow publicly; keep MutableStateFlow private.

• A ViewModel's scope is tied to a logical screen, not an Activity instance.

• Don't put business logic in a ViewModel — that belongs in the domain layer or a UseCase.
```

---

## Common Misconception                       ← [OPTIONAL] Only when a wrong idea is genuinely widespread

A common misconception is:

```text
ViewModel = The Screen
```

Incorrect.

```text
Activity / Fragment
↓
Displays The Screen

ViewModel
↓
Owns The Screen State
```

The UI and its state have different responsibilities.
A ViewModel can survive the destruction of the Activity that displays it.

---

## A Natural Question                         ← [REQUIRED] Lead to the next concept

If the Activity is destroyed, how does Android reconnect
the new Activity with the existing ViewModel?

```text
Old Activity Destroyed
↓
New Activity Created
↓
Same ViewModel — but who preserved it?
```

That question leads us to the next chapter:

```text
ViewModelStore
```

---

## Revision                                   ← [REQUIRED] Summary for review

### Core Idea

```text
ViewModel = Owner Of Screen State
```

### Mental Model

```text
Activity / Fragment
↓
Displays
---
ViewModel
↓
Owns State — survives configuration change
```

### Production Recognition

```kotlin
class SearchViewModel : ViewModel() {
    private val _state = MutableStateFlow(...)
    val state = _state.asStateFlow()
}
```

```text
MutableStateFlow private, StateFlow public.
This ViewModel owns state that survives config changes.
```

### Previous Concept

Configuration Changes

### Next Concept

ViewModelStore
```

---

## Common Pitfalls

**Starting with the API instead of the problem.**
If your first section introduces a class or annotation, reorder. Problem always comes first.

```
BAD:
# StateFlow
StateFlow is a data flow API that emits state changes...

GOOD:
# StateFlow

## Looking Back
[Natural question from previous concept]

## The Problem
[Pain that StateFlow solves]
```

---

**One concept, multiple big ideas.**
`# Android Lifecycle & Configuration Changes & ViewModel Lifecycle` is three documents. If you can't summarize in one sentence, split.

```
BAD:
# Android Lifecycle & Configuration Changes & ViewModel Lifecycle

GOOD (split into 3):
- Android Lifecycle (when things change)
- Configuration Changes (why Activity recreates)
- ViewModel Lifecycle (why ViewModel survives)
```

---

**Manufactured curiosity at the end.**

```
BAD:
"But wait, there's more! You'll want to learn about ViewModelStore..."

GOOD:
## A Natural Question
If the Activity is destroyed, how does Android reconnect
the new Activity with the existing ViewModel?

That question leads us to the next chapter: ViewModelStore
```

---

**Production Code without "How To Read It."**
Production Code that ends at the code block teaches syntax. The reader needs to learn *how to think* about the code, not just what it does.

```
BAD:
## Production Code
```kotlin
class UserViewModel(private val repository: UserRepository) : ViewModel() { ... }
```
[Document continues]

GOOD:
## Production Code
```kotlin
class UserViewModel(private val repository: UserRepository) : ViewModel() { ... }
```

### How To Read It
Don't read: constructor takes a repository
Read: ViewModel receives application data, doesn't own it — Repository does
```

---

**Assuming knowledge not yet taught.**

```
BAD:
"The coroutine suspension mechanism handles context switching..."
[Written assuming the reader knows what suspension is]

GOOD:
[Either explain suspension first, or link to DOC 2 — Suspend Function]
```

---

**Examples that all teach the same thing.**
Four examples showing the same concept don't build four times the understanding. Each example should add something new — a standard approach, an important variation, an edge case.

---

**Over-explaining basics.**

```
BAD:
"In Kotlin, a class is declared with the 'class' keyword.
For example: class MyClass { }
A class can have properties..."
[200 words explaining basics]

GOOD:
[Trust that the reader knows basic Kotlin. Link if genuinely uncertain.]
```

---

## Getting Unstuck

**"I have too many ideas for one document."**
Split it. One primary question, one document. If you cannot summarize the concept in one sentence, you have multiple documents.

**"The production code doesn't fit the mental model."**
This is the signal to split into `01-mental-model.md` + `02-production-code.md`. The mental model and the production complexity have diverged enough to warrant separate treatment.

**"I keep re-explaining the same thing."**
Cut two-thirds of it. Over-explanation is the most common Atlas writing failure. Trust that the reader read the previous document.

**"I don't know how to write 'How To Read It.'"**
Ask: what does an experienced engineer see when reading this code that a junior doesn't? The experienced engineer sees responsibilities and ownership. The junior sees APIs and method calls. "How To Read It" closes that gap.

**"The natural question doesn't feel natural."**
It probably isn't. Check the master-index — does the next concept follow directly from this one? If the dependency chain is wrong, the transition will always feel forced. Recheck the sequence.

**"My document is 800+ lines."**
You have multiple big ideas. Split it. Or you are over-explaining — cut 30% and re-read.

**"I don't know if a section should be included."**
Ask: can I remove this section without reducing understanding? If yes, remove it.

---

## Exercises (Self-Test Questions)

Self-test questions are not written inside concept documents. They belong in a separate Exercises folder, organized by book:

```
exercises/
  book-1-foundations/
  book-2-architecture-and-ui/
  book-3-production-systems/
  book-4-mastery-optimization/
  book-5-learning-from-experience/
```

Each exercise file covers one document. Three question levels:
- **Recognition:** Can you identify this pattern in unfamiliar code?
- **Application:** Can you apply this pattern to a new scenario?
- **Judgment:** Can you reason about when to violate the rule — and why that might be intentional?

Readers pursue them separately, on their own schedule. They are not a required section of the concept document.

---

That is the guide.

The 6 rules, the metadata block, the required sections, the optional tools, the examples — all of it is here. A reader who follows this document cold should be able to write any Atlas concept that fits the curriculum, in the right voice, at the right depth, with the right structure.

If something feels unclear while writing, the "Getting Unstuck" section has the answer.
If something feels like it should be included but you're not sure, apply the filter: can the reader understand the concept without it? If yes, cut it.
