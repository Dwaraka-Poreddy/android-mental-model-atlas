# LayoutNode

## Observation

Earlier,

we learned that every composable call becomes part of the **Composition Tree**.

The Composition Tree helps Compose understand the structure of the composable hierarchy.

A natural question appears.

```text
If The Composition Tree Tracks Composable Calls,

↓

What Actually Represents The UI?
```

After all,

a composable function eventually finishes executing.

For example,

```kotlin
Text("Hello")
```

returns almost immediately.

The function is gone.

Yet,

the text is still visible on the screen.

Another question appears.

```text
If The Composable Already Finished,

↓

What Is Actually Representing The UI Now?
```

---

## Connecting It To Compose

Composable functions are temporary.

They execute,

describe the UI,

and then return.

The runtime therefore needs another structure that continues to represent the UI after the composable has finished executing.

That structure is called a **LayoutNode**.

```text
Composable

↓

Describes UI

↓

LayoutNode Represents UI
```

The composable describes the UI.

The LayoutNode keeps representing that UI inside the Compose Runtime.

---

## Composition Tree vs UI Tree vs LayoutNode Tree

At this point,

it's easy to get confused because we've discussed several different "trees."

Let's clarify them.

| Tree | Represents | Purpose | Exists As |
|------|------------|---------|-----------|
| **Composition Tree** | Composable function hierarchy | Helps Compose understand composable relationships and recomposition | Runtime data structure |
| **UI Tree** | The visual hierarchy of the screen | A mental model for understanding the UI | Conceptual model |
| **LayoutNode Tree** | Internal representation of the UI | What Compose actually measures, lays out and draws | Runtime data structure |

The important thing to understand is this:

```text
UI Tree

↓

Concept

------------------------

LayoutNode Tree

↓

Runtime Implementation
```

Throughout the earlier documents,

we used the term **UI Tree** because it was enough to build a mental model.

Now that we're studying the Compose Runtime,

we can use the more precise term:

```text
LayoutNode Tree
```

because that's the actual data structure Compose uses internally.

---

## Why Doesn't The Composition Tree Represent The UI?

This is another natural question.

```text
Why Can't The Composition Tree Represent The UI By Itself?
```

Because the two trees have different responsibilities.

The Composition Tree exists to understand:

- which composables exist
- how they're nested
- how recomposition should work

The LayoutNode Tree exists to represent:

- the UI hierarchy
- sizes
- positions
- drawing information

Although the two trees are closely related,

they solve different problems.

---

## Why LayoutNodes Exist

Composable functions don't live forever.

They execute,

return,

and disappear from the call stack.

The UI,

however,

must continue to exist.

Compose therefore needs a persistent representation that it can:

- measure
- position
- redraw
- update during recomposition

That's exactly what a LayoutNode provides.

---

## What Does A LayoutNode Store?

A LayoutNode doesn't store pixels.

Instead,

it stores the information Compose needs to build the final UI.

For example,

a LayoutNode knows things such as:

- its children
- its measured size
- its position
- information required during layout and drawing

We'll learn how these values are calculated in the next few chapters.

---

## Why Should I Care As A Developer?

Normally,

you'll never create a LayoutNode yourself.

It's an internal runtime class.

However,

understanding that the UI is represented by LayoutNodes explains many things you'll encounter later, including:

- measurement
- layout
- drawing
- recomposition
- performance optimizations

Nearly every operation in the rendering pipeline works on LayoutNodes,

not directly on composable functions.

---

## What LayoutNodes Do NOT Do

LayoutNodes do **not**:

```text
Execute Composable Functions

↓

Observe State

↓

Perform Recomposition
```

Their responsibility is simply:

```text
Represent The UI Inside The Compose Runtime
```

---

## Production Recognition

When you write:

```kotlin
Text("Hello")
```

don't think:

```text
This composable continues to exist while the text is visible.
```

think:

```text
The composable described the UI. The LayoutNode now represents it.
```

---

## Revision

### Core Idea

```text
LayoutNode

↓

Internal Representation Of The UI
```

### Mental Model

```text
Composable

↓

Description

↓

LayoutNode

↓

Persistent UI Representation
```

### The Three Trees

| Tree | Think Of It As |
|------|----------------|
| **Composition Tree** | Composable hierarchy |
| **UI Tree** | Conceptual visual hierarchy |
| **LayoutNode Tree** | Runtime implementation of the UI Tree |

### Why It Exists

```text
Composable Functions

Are Temporary

↓

The UI Needs A Persistent Representation
```

---

## One Remaining Question

The LayoutNode Tree now represents the UI.

A natural question appears.

```text
How Does Compose Decide How Big Each LayoutNode Should Be?
```

That leads us to the next concept.

```text
Measure
```
