# Draw

## Observation

In the previous chapters,

we learned that every LayoutNode is:

- measured
- positioned

A natural question appears.

**How does it finally become visible on the screen?**

Imagine this composable.

```kotlin
Box(
    modifier = Modifier
        .size(100.dp)
        .background(Color.Blue)
)
```

At this point,

Compose already knows:

- the LayoutNode exists
- its width is 100dp
- its height is 100dp
- where it should appear

Another question appears.

**Where does the blue square actually get painted?**

Knowing an element's size and position doesn't automatically create pixels.

Something still needs to paint the UI.

---

## A Thought Experiment

Imagine an artist painting on a canvas.

Before painting,

they already know:

- the size of the canvas
- where every object belongs

But the canvas is still blank.

The final step is picking up the brush and painting.

Only then does the picture become visible.

Compose follows exactly the same idea.

---

## Connecting It To Compose

Once every LayoutNode has:

- a size
- a position

Compose performs one final phase:

**Draw**

```text
Measure
(How big?)

↓

Layout
(Where?)

↓

Draw
(What should it look like?)
```

During this phase,

every LayoutNode paints itself onto the screen.

---

## What Happens During Draw?

The Draw phase converts the UI into pixels.

For example,

it draws:

- text
- backgrounds
- borders
- images
- shapes
- shadows

Everything you actually see on the screen is produced during this phase.

---

## Why Draw Exists

Imagine Compose stopped after Layout.

It would know:

- every element's size
- every element's position

But the screen would still be blank.

Draw solves that final problem.

It transforms the internal UI representation into visible pixels.

---

## Why Should I Care As A Developer?

Understanding Draw explains many everyday Compose APIs.

For example,

these primarily affect drawing:

- `background()`
- `border()`
- `shadow()`
- `drawBehind()`
- `drawWithContent()`
- `Canvas`

Notice something important.

These APIs don't usually affect:

- measurement
- layout

They affect **how the UI is painted**.

That's why changing a background color doesn't trigger a new layout.

---

## Measure vs Layout vs Draw

Each phase answers a different question.

| Phase | Question It Answers |
|--------|----------------------|
| **Measure** | How big should it be? |
| **Layout** | Where should it be placed? |
| **Draw** | What should it look like? |

Whenever you're debugging a Compose UI,

ask yourself:

- Is the size wrong?
- Is the position wrong?
- Is the appearance wrong?

Those questions naturally point you toward the correct phase of the rendering pipeline.

---

## What Draw Does NOT Do

Draw does **not**:

- determine size
- determine position
- execute composable functions

Its only responsibility is:

> **Paint the LayoutNode onto the screen.**

---

## Production Recognition

When you write:

```kotlin
Modifier.background(Color.Blue)
```

think:

> This affects the **Draw** phase.

When you write:

```kotlin
Modifier.border(2.dp, Color.Black)
```

think:

> This changes how the LayoutNode is painted.

Neither of these changes the LayoutNode's measured size or position.

---

## Revision

### Core Idea

```text
Draw

↓

Convert UI Into Pixels
```

### Mental Model

```text
Measure
(How big?)

↓

Layout
(Where?)

↓

Draw
(What should it look like?)
```

### Measure vs Layout vs Draw

| Phase | Responsibility |
|--------|----------------|
| **Measure** | Determine size |
| **Layout** | Determine position |
| **Draw** | Paint pixels |

### Why It Exists

> Transform the measured and positioned UI into visible pixels.

### Developer Perspective

- Wrong size? → Think **Measure**
- Wrong position? → Think **Layout**
- Wrong appearance? → Think **Draw**

---

## One Remaining Question

We've now seen how Compose:

- represents the UI
- measures it
- positions it
- draws it

A natural question appears.

**How are all these LayoutNodes actually created, updated and connected to the underlying UI during recomposition?**

That leads us to the final concept of the rendering pipeline.

```text
Applier
```
