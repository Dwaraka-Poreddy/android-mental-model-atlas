# Applier

## Observation

At this point,

we've learned that Compose:

- understands the UI
- remembers the UI
- observes state changes
- performs recomposition
- measures the UI
- positions the UI
- draws the UI

A natural question appears.

**Who actually builds and updates the LayoutNode Tree?**

After all,

recomposition tells Compose **what** changed.

But something still has to update the runtime's internal UI representation.

---

## A Thought Experiment

Imagine you're editing a document.

You type:

```text
Hello
```

The editor first determines what changed.

For example,

it realizes:

- insert new text
- remove old text
- replace existing text

Knowing what changed isn't enough.

Someone still has to perform those operations on the document.

Compose has the same problem.

---

## Connecting It To Compose

During recomposition,

Compose figures out what has changed.

The **Applier** is responsible for taking those changes and updating the LayoutNode Tree.

Think of it like this.

```text
Recomposition

↓

Changes Identified

↓

Applier

↓

Update LayoutNode Tree
```

The Applier doesn't decide what changed.

It simply applies those changes.

---

## Separation Of Responsibilities

One of the strengths of the Compose Runtime is that different components have different responsibilities.

| Component | Responsibility |
|----------|----------------|
| **Recomposition** | Determine what changed |
| **Applier** | Apply those changes to the LayoutNode Tree |
| **Measure** | Determine size |
| **Layout** | Determine position |
| **Draw** | Paint pixels |

Each phase has one clear responsibility.

---

## Why Does The Applier Exist?

A natural question appears.

**Why doesn't recomposition update the LayoutNode Tree directly?**

Because these are two different responsibilities.

Recomposition is responsible for determining **what** needs to change.

The Applier is responsible for performing those changes.

Keeping these responsibilities separate makes the runtime simpler,

cleaner,

and easier to maintain.

---

## Why Should I Care As A Developer?

Unlike concepts such as Measure,

Layout,

or Stability,

you'll rarely think about the Applier during day-to-day Compose development.

However,

understanding that it exists completes the mental model of how Compose updates the UI.

Without the Applier,

there would be a missing step.

```text
Recomposition

↓

?

↓

Updated LayoutNode Tree
```

The Applier fills that gap.

---

## What The Applier Does NOT Do

The Applier does **not**:

- execute composable functions
- observe state
- perform recomposition
- measure LayoutNodes
- position LayoutNodes
- draw pixels

Its only responsibility is:

> **Apply the changes discovered during recomposition to the LayoutNode Tree.**

---

## Production Recognition

Most Compose developers never interact with the Applier directly.

Instead,

think of it as the runtime component that keeps the LayoutNode Tree synchronized with the latest recomposition results.

---

## Revision

### Core Idea

```text
Recomposition

↓

Determine Changes

↓

Applier

↓

Update LayoutNode Tree
```

### Mental Model

The Applier is the runtime component that updates the LayoutNode Tree after recomposition.

### Why It Exists

Separate:

- determining changes

from

- applying changes

---

## Completing The Runtime Pipeline

We've now completed the complete journey from a state change to visible pixels.

```text
State Changed
      │
      ▼
Snapshot System
      │
      ▼
Recomposition
      │
      ▼
Composition Tree Updated
      │
      ▼
Applier
      │
      ▼
LayoutNode Tree Updated
      │
      ▼
Measure
      │
      ▼
Layout
      │
      ▼
Draw
      │
      ▼
Pixels
```

This pipeline ties together everything we've learned throughout **Doc 7**.

---

## Looking Ahead

At this point,

we understand **how the Compose Runtime works internally**.

A natural question appears.

**How do experienced Android developers write Compose code that works *with* this runtime instead of against it?**

That's exactly what we'll explore in the next document.

```text
DOC 8

Production Compose Patterns
```
