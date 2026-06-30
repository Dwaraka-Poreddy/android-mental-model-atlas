# DOC 7 — Inside Compose Runtime

**Purpose:** How Compose Understands, Remembers And Updates Your UI

This document teaches how the Compose runtime works internally. Understanding these internals helps you reason about performance and write efficient Compose code.

## Concepts Included

### Part 1 — Runtime Overview
- [x] [From Composable Functions To Runtime](./01-from-composable-functions-to-runtime.md) — How @Composable functions map to runtime concepts
- [x] [Composer](./02-composer.md) — The orchestrator that coordinates composition and recomposition
- [x] [Composition Tree](./03-composition-tree.md) — The tree of composable nodes maintained by the runtime
- [x] [UI Tree](./04-ui-tree.md) — The rendering tree that the compositor draws from
- [x] [Composition Tree vs UI Tree](./05-composition-tree-vs-ui-tree.md) — Why two separate trees exist and how they relate

### Part 2 — Remembering UI
- [x] [Slot Table](./06-slot-table.md) — The data structure Compose uses to persist composable state between recompositions
- [x] [Groups](./07-groups.md) — The unit of identity in the Slot Table that maps composable call sites to slots
- [x] [Anchors](./08-anchors.md) — How Compose remembers positions in the Slot Table across recompositions
- [x] [How remember Works](./09-how-remember-works.md) — What happens inside a remember {} block at the runtime level
- [x] [Key Changes](./10-keys.md) — How the key() function changes composable identity and forces recomposition

### Part 3 — State Observation
- [x] [Snapshot System](./11-snapshot-system.md) — The isolation mechanism that makes Compose state changes safe across threads
- [x] [SnapshotState](./12-snapshot-state.md) — State objects that integrate with the snapshot system for automatic observation
- [x] [State Read Tracking](./13-state-read-tracking.md) — How Compose records which composables read which state objects
- [x] [State Write Tracking](./14-state-write-tracking.md) — How Compose detects state mutations and schedules recomposition

### Part 4 — Recomposition Internals
- [x] [How Recomposition Works](./15-recomposition-algorithm.md) — The algorithm Compose uses to decide what to re-run after a state change
- [x] [Restart Groups](./16-restart-groups.md) — The recomposition unit — the subtree that re-executes when its state changes
- [x] [Skipping](./17-skipping.md) — How Compose skips re-running a composable when its parameters haven't changed
- [x] [Stability](./18-stability.md) — What makes a type stable and how stability enables skipping

### Part 5 — Rendering Pipeline
- [x] [LayoutNode](./19-layout-node.md) — The node type that holds layout and drawing instructions in the UI tree
- [x] [Measure](./20-measure.md) — How Compose measures each LayoutNode to determine its size
- [x] [Layout](./21-layout.md) — How Compose positions each LayoutNode relative to its parent
- [x] [Draw](./22-draw.md) — How Compose issues drawing instructions from the layout tree to the Canvas
- [x] [Applier](./23-applier.md) — The bridge between the composition tree and the rendering tree

## Reading Order

Concepts should be read in the order listed because:

1. **Understand the architecture** (Runtime, Composer, Trees)
2. **Understand memory** (Slot Table, Groups, How remember works)
3. **Understand observation** (Snapshot System, State Tracking)
4. **Understand updates** (Recomposition, Restart Groups, Skipping)
5. **Understand rendering** (LayoutNode, Measure, Layout, Draw)

## How This Fits Into The Atlas

This document answers: **How does Compose actually work internally?**

Understanding these details enables you to write performant Compose code and debug issues effectively.

The next document, **DOC 8 — Production Compose Patterns**, teaches how experienced engineers use these insights to build production applications.

---

**Start with:** [From Composable Functions To Runtime](./01-from-composable-functions-to-runtime.md)
