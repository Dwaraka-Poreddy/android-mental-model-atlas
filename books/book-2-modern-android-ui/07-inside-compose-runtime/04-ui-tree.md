# UI Tree

## Observation

In the previous chapter, we learned that the Composer builds a **Composition Tree** to understand the relationships between our composable functions.

```text
Composable Functions

↓

Composer

↓

Composition Tree
```

A simple question appears.

```text
The Runtime Now Understands My Composable Hierarchy.

↓

But How Does That Become Something Android Can Display?
```

---

## A Thought Experiment

Imagine an architect designing a house.

The architect first creates a blueprint.

```text
Blueprint
```

The blueprint explains:

- where the rooms are
- how they connect
- what the house should look like

But a construction company doesn't build directly from that conceptual description.

It creates a construction plan containing everything needed to actually build the house.

```text
Blueprint

↓

Construction Plan

↓

House
```

One describes the structure.

The other represents what will actually be built.

---

## Connecting It To Compose

Compose works in a similar way.

The Composition Tree describes the hierarchy of composables.

The runtime then creates another structure that represents the actual UI it will build.

Think of it as:

```text
Composition Tree

↓

UI Tree

↓

Screen
```

The UI Tree is much closer to what eventually appears on the screen.

---

## What The UI Tree Represents

The UI Tree contains the runtime's representation of the actual UI.

Instead of describing relationships between composable functions,

it represents the UI elements that will eventually be measured,

positioned,

and drawn on the screen.

Think of it as answering questions like:

```text
What Needs To Be Displayed?

↓

Where Will It Appear?
```

---

## Why The UI Tree Exists

The runtime eventually needs something it can turn into pixels.

The Composition Tree alone isn't enough.

So the runtime creates another structure specifically for representing the UI itself.

```text
Composable Functions

↓

Composition Tree

↓

UI Tree

↓

Screen
```

Each step moves one level closer to what the user actually sees.

---

## What The UI Tree Does NOT Represent

The UI Tree does **not** represent the relationships between composable functions.

That responsibility belongs to the Composition Tree.

We'll compare these two trees directly in the next chapter.

For now,

remember this simple idea.

```text
Composition Tree

↓

Composable Hierarchy

------------------------

UI Tree

↓

Actual UI Representation
```

---

## Production Recognition

When you think about the UI displayed on the screen,

don't imagine Compose repeatedly executing your composable functions.

Instead, think:

```text
Composable Functions

↓

Composition Tree

↓

UI Tree

↓

Screen
```

The runtime works with these internal structures,

not directly with your source code.

---

## Revision

### Core Idea

```text
UI Tree

↓

Runtime's Representation Of The Actual UI
```

### Mental Model

```text
Composable Functions

↓

Composition Tree

↓

UI Tree

↓

Screen
```

### What It Represents

```text
UI Elements

↓

Measure

↓

Layout

↓

Draw
```

### Production Recognition

```text
UI Tree
```

↓

```text
What Compose Will Eventually Display
```

---

## One Remaining Question

We now know that the Compose Runtime maintains both a **Composition Tree** and a **UI Tree**.

A natural question appears.

```text
Why Does Compose Need Two Trees?
```

That leads us to the next concept.

```text
Composition Tree vs UI Tree
```
