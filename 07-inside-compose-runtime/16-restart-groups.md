# Restart Groups

## Observation

In the previous chapter,

we learned that Compose follows a workflow to perform recomposition.

A simple question appears.

```text
State Changed.

↓

Compose Needs To Recompose.

↓

But Where Does It Start?
```

Imagine this screen.

```kotlin
@Composable
fun Screen() {

    Header()

    Counter()

    Footer()

}
```

Now suppose:

```kotlin
count++
```

executes.

We already know:

- `Counter()` depends on `count`
- `Header()` doesn't
- `Footer()` doesn't

A natural question appears.

```text
Counter() Needs To Recompose.

↓

Does Compose Restart From Screen()?

↓

Or Can It Start Closer To Counter()?
```

---

## A Thought Experiment

Imagine you're editing a book.

You discover a typo on page 143.

Would you start reading again from page 1?

Of course not.

Instead,

you open the book directly to page 143.

```text
Book

↓

Page 143

↓

Continue Editing
```

You restart from the relevant place,

not from the beginning.

---

## Connecting It To Compose

Compose works in a very similar way.

When state changes,

it doesn't always restart recomposition from the root of the UI.

Instead,

it looks for a safe place where recomposition can begin.

That place is called a **Restart Group**.

Think of it like this.

```text
Screen()

↓

Header()

↓

Counter() ← Restart Here

↓

Footer()
```

Instead of starting over,

Compose resumes work from the nearest restart point.

---

## A New Mental Model

Think of a Restart Group as a checkpoint.

```text
State Changed

↓

Find Restart Group

↓

Resume Recomposition From There
```

Restart Groups allow the runtime to begin recomposition much closer to where the change actually occurred.

---

## Why Restart Groups Exist

Imagine Compose always restarted from:

```text
Screen()
```

Even if only:

```text
Counter()
```

needed to update,

Compose would do unnecessary work.

Restart Groups allow the runtime to restart much closer to the affected composable,

making recomposition more efficient.

---

## What Restart Groups Do NOT Do

Restart Groups do **not**:

```text
Observe State

↓

Store Remembered Values

↓

Render The UI
```

Their responsibility is much simpler.

```text
Provide A Safe Place To Restart Recomposition
```

---

## Production Recognition

When you think:

```text
Counter() Needs To Recompose
```

don't think:

```text
Compose Starts From The Root
```

think:

```text
Compose Finds The Nearest Restart Group

↓

Continues From There
```

---

## Revision

### Core Idea

```text
Restart Groups

↓

Restart Recomposition Closer To The Change
```

### Mental Model

```text
State Changed

↓

Find Restart Group

↓

Resume Recomposition
```

### Why They Exist

```text
Avoid Restarting From The Root
```

### Production Recognition

```text
Restart Group
```

↓

```text
A Safe Checkpoint For Recomposition
```

---

## One Remaining Question

Restart Groups tell Compose **where** to begin recomposition.

A natural question appears.

```text
Once Recomposition Begins,

↓

Does Compose Always Run Every Composable Again?
```

That leads us to the next concept.

```text
Skipping
```
