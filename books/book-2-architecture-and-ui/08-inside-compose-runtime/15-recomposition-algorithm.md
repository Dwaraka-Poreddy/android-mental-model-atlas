# Recomposition Algorithm

## Where We Are In The Journey

Over the last three parts,

we've gradually uncovered how the Compose Runtime works.

```text
Part 1

↓

Understand The UI

------------------------

Part 2

↓

Remember The UI

------------------------

Part 3

↓

Observe State Changes
```

Compose now has everything it needs.

It:

- understands the UI
- remembers the UI
- observes state changes

A natural question appears.

```text
Compose Now Knows Everything It Needs.

↓

What Does It Actually Do Next?
```

That's exactly what the **Recomposition Algorithm** answers.

---

## A Thought Experiment

Imagine a company receives a bug report.

The report says:

```text
Login Screen

↓

Login Button Is Broken
```

The engineering manager doesn't ask every team to rebuild the entire application.

Instead,

the manager follows a process.

```text
Identify The Problem

↓

Find The Responsible Team

↓

Fix Only That Part
```

The work happens in a sequence of steps,

not all at once.

---

## Connecting It To Compose

Compose works in a very similar way.

When state changes,

it doesn't immediately start rebuilding the UI.

Instead,

it follows a workflow.

Think of it like this.

```text
State Changed

↓

Change Detected

↓

Find Affected Composable

↓

Recompose

↓

Update The UI
```

This workflow is called the **Recomposition Algorithm**.

---

## A New Mental Model

Recomposition is not a single action.

It's a sequence of steps performed by the runtime.

```text
State Changes

↓

Snapshot System Detects The Change

↓

Find Affected Composable

↓

Run Composable Again

↓

Update The UI
```

Every step builds on the concepts we've already learned.

---

## Why The Recomposition Algorithm Exists

Imagine Compose skipped this workflow.

Every state change would force it to rebuild the entire screen.

Instead,

the runtime follows a well-defined process that updates only the parts of the UI affected by the change.

That's one of the reasons Compose is efficient.

---

## What The Recomposition Algorithm Does NOT Explain

This chapter describes the overall workflow.

It does **not** yet explain:

```text
Where Recomposition Starts

↓

How Compose Skips Work

↓

How Compose Decides Something Is Safe To Skip
```

We'll explore each of those in the upcoming chapters.

---

## Production Recognition

When you write:

```kotlin
count++
```

don't think:

```text
Compose Immediately Updates The UI
```

think:

```text
State Changed

↓

Runtime Runs The Recomposition Workflow
```

---

## Revision

### Core Idea

```text
Recomposition

↓

A Runtime Workflow
```

### Mental Model

```text
State Changed

↓

Detected

↓

Find Affected Composable

↓

Recompose

↓

Update The UI
```

### Why It Exists

```text
Update Only What Changed
```

### Production Recognition

```kotlin
count++
```

↓

```text
State Changed

↓

Runtime Performs The Recomposition Workflow
```

---

## One Remaining Question

We now know that Compose follows a workflow to perform recomposition.

A natural question appears.

```text
Where Does That Workflow Actually Begin?
```

How does Compose know exactly **where** to restart recomposition?

That leads us to the next concept.

```text
Restart Groups
```
