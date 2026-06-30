# Layout

## Observation

In the previous chapter,

we learned that every LayoutNode is first measured.

After measurement,

Compose knows the size of every UI element.

A natural question appears.

Every LayoutNode knows its size.

**Where should it appear on the screen?**

Imagine this composable.

```kotlin
Column {

    Box(...)

    Box(...)

    Box(...)

}
```

Suppose measurement has already completed.

Compose now knows:

- Box 1 → 100 × 50
- Box 2 → 100 × 80
- Box 3 → 100 × 40

Another question appears.

Where should each box be placed?

Knowing an element's size doesn't automatically tell Compose where it belongs.

---

## A Thought Experiment

Imagine you're arranging furniture in a room.

You've already measured everything.

- Sofa → 220 cm wide
- Table → 120 cm wide
- Chair → 60 cm wide

Great.

But the room is still empty.

The next step is deciding:

- The sofa goes here.
- The table goes there.
- The chair goes beside the table.

Only then does the room begin to take shape.

Compose follows exactly the same process.

```text
Measure

↓

Place
```

---

## Connecting It To Compose

Once measurement is complete,

every LayoutNode knows its size.

The next step is called **Layout**.

During this phase,

Compose decides where each LayoutNode should be positioned.

```text
LayoutNode

↓

Layout

↓

Position
```

Measurement answers:

> **How big?**

Layout answers:

> **Where?**

---

## Who Decides The Position?

This is one of the most important ideas in Compose.

Children do **not** decide where they appear.

Parents do.

Think of it as another conversation.

```text
Parent

↓

I know your size.

↓

Here is your position.

↓

Child
```

For example,

a `Column` might position its children like this:

```text
Child 1 → y = 0

Child 2 → y = 50

Child 3 → y = 130
```

The children simply accept those positions.

---

## Why Does The Parent Decide?

A natural question appears.

**Why doesn't each child decide its own position?**

Imagine every child chose its own location.

Two children might decide to occupy the same place,

while another part of the screen remains empty.

The parent is responsible for arranging all of its children,

so it has the complete picture.

That's why layout is always driven by the parent.

---

## Why Layout Exists

Imagine Compose stopped after measurement.

It would know:

- every element's width
- every element's height

But every LayoutNode would still exist at the same origin.

Without positioning,

the UI couldn't be arranged into a meaningful screen.

Layout solves exactly that problem.

---

## Why Should I Care As A Developer?

Understanding layout explains many everyday Compose APIs.

For example,

these affect **where** elements are placed:

- `Row`
- `Column`
- `Box`
- `Arrangement`
- `Alignment`
- `Modifier.offset()`

Notice that none of them change an element's measured size.

They only influence its position.

Understanding this distinction makes many layout issues much easier to debug.

---

## Measure vs Layout

These two phases solve completely different problems.

| Measure | Layout |
|---------|--------|
| Determines **size** | Determines **position** |
| Answers **"How big?"** | Answers **"Where?"** |
| Child chooses a size within constraints | Parent chooses the final position |

Whenever you're debugging a UI,

first ask yourself:

> **Is this a measurement problem, or a layout problem?**

That simple question often narrows the problem down immediately.

---

## What Layout Does NOT Do

Layout does **not**:

- determine sizes
- draw pixels
- apply colors or styling

Its only responsibility is:

> **Position each LayoutNode on the screen.**

---

## Production Recognition

When you use:

```kotlin
Modifier.offset(16.dp)
```

think:

> This changes **where** the LayoutNode is placed.

When you change:

```kotlin
verticalArrangement = Arrangement.SpaceBetween
```

think:

> This changes **how the parent positions its children**.

Neither of these changes the measured size.

---

## Revision

### Core Idea

```text
Measure

↓

How Big?

------------------------

Layout

↓

Where?
```

### Mental Model

```text
Parent

↓

Choose Positions

↓

Children
```

### Measure vs Layout

| Measure | Layout |
|---------|--------|
| Size | Position |
| Child chooses within constraints | Parent decides placement |

### Why It Exists

> Arrange the measured UI into its correct positions on the screen.

### Developer Perspective

- Wrong size? → Think **Measurement**.
- Wrong position? → Think **Layout**.

---

## One Remaining Question

At this point,

every LayoutNode knows:

- its size
- its position

A natural question appears.

**How does it finally become pixels on the screen?**

That leads us to the next concept.

```text
Draw
```
