# Measure

## Observation

In the previous chapter,

we learned that the LayoutNode Tree is the runtime's representation of the UI.

A natural question appears.

```text
The UI Now Exists As LayoutNodes.

↓

How Does Compose Know How Big Each LayoutNode Should Be?
```

Imagine these composables.

```kotlin
Text("Hello")
```

```kotlin
Button(
    onClick = { }
) {
    Text("Submit")
}
```

```kotlin
Box(
    modifier = Modifier.fillMaxSize()
)
```

Another question appears.

```text
Where Did Their Sizes Come From?
```

Nobody explicitly wrote:

```kotlin
width = 87.dp
height = 42.dp
```

Yet every UI element ends up with a size.

How?

---

## A Thought Experiment

Imagine you're moving furniture into a new house.

Before deciding where to place the sofa,

you first need to know:

- How wide is it?
- How deep is it?
- How tall is it?

Without knowing its size,

placing it in the room would be impossible.

The process naturally happens in two steps.

```text
Measure

↓

Place
```

Compose follows exactly the same idea.

---

## Connecting It To Compose

Every LayoutNode must first determine its size.

This process is called **Measure**.

```text
LayoutNode

↓

Measure

↓

Width Height
```

Only after a LayoutNode knows its size,

can Compose decide where it should appear on the screen.

---

## The Most Important Idea — Constraints

A natural question appears.

```text
How Does A LayoutNode Choose Its Size?
```

The answer is:

It doesn't choose completely freely.

Instead,

its parent gives it **constraints**.

Think of it as a conversation.

```text
Parent

↓

Here Are Your Constraints

↓

Child

↓

Here's My Measured Size
```

For example,

the parent might say:

```text
You may be Between 100dp And 300dp Wide.
```

The child must choose a size within those limits.

This parent-child contract is the foundation of Compose's layout system.

---

## An Important Realization

Notice something subtle.

The parent does **not** tell the child:

```text
Your Width Is 200dp
```

Instead,

it says:

```text
Choose Any Width Between 100dp And 300dp
```

The child then decides its measured size within those constraints.

This distinction is extremely important.

Compose layouts are built around **constraints**, not fixed sizes.

---

## Why Measure Exists

Imagine Compose skipped the measurement step.

It would know:

- what UI exists

but it wouldn't know:

- how much space each element needs.

Without sizes,

layout would be impossible.

Measurement solves exactly that problem.

---

## Why Should I Care As A Developer?

Understanding measurement explains many everyday Compose behaviors.

For example,

these modifiers all influence measurement.

- `fillMaxSize()`
- `fillMaxWidth()`
- `fillMaxHeight()`
- `wrapContentSize()`
- `weight()`

They don't directly change the UI.

Instead,

they influence how a LayoutNode is measured.

Understanding this makes many sizing issues much easier to reason about.

---

## What Measure Does NOT Do

Measurement does **not** decide:

- where the UI appears
- what gets drawn
- what color something is

Its only responsibility is:

```text
Determine The Size Of Each LayoutNode
```

The next step,

deciding **where** those LayoutNodes should be placed,

is called **Layout**.

---

## Production Recognition

When you write:

```kotlin
Modifier.fillMaxWidth()
```

don't think:

```text
This Immediately Makes The View Wider.
```

think:

```text
This Changes The Constraints Used During Measurement.
```

Likewise,

when you write:

```kotlin
Modifier.wrapContentSize()
```

think:

```text
This Changes How The Child Chooses Its Measured Size.
```

---

## Revision

### Core Idea

```text
Measure

↓

Determine The Size Of Each LayoutNode
```

### Mental Model

```text
Parent

↓

Constraints

↓

Child

↓

Measured Size
```

### The Key Insight

```text
Parents Provide Limits.

↓

Children Choose A Size Within Those Limits.
```

### Why It Exists

```text
Know How Much Space Every UI Element Needs
```

### Developer Perspective

```text
fillMaxWidth()

↓

Changes Measurement

------------------------

wrapContentSize()

↓

Changes Measurement

------------------------

weight()

↓

Changes Measurement
```

---

## One Remaining Question

Every LayoutNode now knows its size.

A natural question appears.

```text
Now That Every LayoutNode Knows Its Size,

↓

How Does Compose Decide Where Each One Should Be Placed?
```

That leads us to the next concept.

```text
Layout
```
