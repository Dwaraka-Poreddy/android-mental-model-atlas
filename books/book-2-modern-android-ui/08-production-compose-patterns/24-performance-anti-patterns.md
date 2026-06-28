# Performance Anti-patterns

## Observation

Imagine two Android developers.

Both know about:

- `remember`
- `derivedStateOf`
- `LazyColumn`
- Keys
- Immutable models
- Stability

Yet one application feels smooth,

while the other constantly janks,

recomposes,

and wastes CPU.

A natural question appears.

> **Why?**

The answer usually isn't that one developer knows more APIs.

It's that one developer avoids the performance anti-patterns that silently defeat the Compose Runtime's optimizations.

---

## Anti-pattern #1 — Optimizing Before Measuring

Suppose someone hears:

> **"Compose recomposition is expensive."**

Immediately,

they begin adding:

- `remember`
- `derivedStateOf`
- `@Immutable`
- `@Stable`

throughout the codebase.

Without measuring anything.

This is premature optimization.

The biggest mistake isn't adding unnecessary optimizations.

It's solving a problem that may not even exist.

Performance work should always begin with one question.

> **Where is the actual bottleneck?**

Measure first.

Optimize second.

Never the other way around.

---

## Anti-pattern #2 — Fighting Recomposition

One of the biggest misconceptions in Compose is:

```text
Recomposition

↓

Bad
```

But everything we've learned throughout the Atlas says otherwise.

Recomposition is normal.

Compose was designed to recompose.

The real goal isn't:

> **Eliminate recomposition.**

The goal is:

> **Eliminate unnecessary recomposition.**

Those are completely different goals.

Trying to stop every recomposition often leads to unnecessary complexity,

without improving performance.

---

## Anti-pattern #3 — Breaking State Ownership

Imagine both a parent and a child own the same piece of state.

```text
Parent

↓

State

↑

Child
```

Now both modify it.

Soon,

it's no longer obvious:

- who owns the state,
- who updates it,
- or why recomposition happened.

Performance problems often begin as architecture problems.

Good state ownership leads to predictable recomposition.

Bad state ownership creates unnecessary work throughout the UI.

---

## Anti-pattern #4 — Using The Wrong Abstraction

Throughout this document,

we've learned many Compose APIs.

Each one exists to solve a specific problem.

Sometimes developers choose an API simply because they know it,

not because it's the right abstraction.

Examples include:

Using:

```kotlin
snapshotFlow
```

when:

```kotlin
derivedStateOf
```

would have been sufficient.

Or using:

```kotlin
produceState
```

when the data already comes from a ViewModel.

Or launching work inside:

```kotlin
LaunchedEffect
```

when a simple event handler using:

```kotlin
rememberCoroutineScope
```

would have been more appropriate.

The result isn't always incorrect.

It's simply unnecessary work.

The best-performing code often comes from choosing the simplest abstraction that solves the problem.

---

## Anti-pattern #5 — Optimizing APIs Instead Of Understanding The Runtime

This is perhaps the most subtle mistake.

Developers often ask:

> **Should I use `remember` here?**

Or:

> **Should I add `@Stable`?**

Or:

> **Should I use a `LazyColumn`?**

These questions focus on APIs.

Experienced engineers ask a different question.

> **What unnecessary work is the runtime currently performing?**

Once that answer is clear,

the appropriate API often becomes obvious.

Sometimes the answer is:

- hoist state,
- use keys,
- derive state,
- make the model immutable.

And sometimes,

the answer is:

> **Don't optimize anything.**

The runtime is already doing an excellent job.

---

## The Runtime's Philosophy

Throughout this part,

we've studied many different features.

At first,

they seem unrelated.

Immutable models.

Stability.

Keys.

Lazy lists.

Different APIs.

Different concepts.

But they all exist for the same reason.

```text
Can The Runtime

Safely Avoid

Unnecessary Work?
```

Everything we've learned is simply a different way of helping the runtime answer that question.

---

## Connecting Everything Together

Notice the progression.

```text
Immutable Models

↓

Predictable State
```

```text
Stability

↓

Trust Comparisons
```

```text
Keys

↓

Preserve Identity
```

```text
Lazy Lists

↓

Avoid Creating

Invisible UI
```

Each optimization reduces work in a different part of the runtime.

But the philosophy never changes.

---

## Thinking Like A Production Engineer

When you notice a performance problem,

don't immediately ask:

> **Which optimization should I apply?**

Instead ask:

> **What unnecessary work is currently happening?**

That one question naturally guides you toward the correct solution.

Without measurement,

optimization is mostly guesswork.

---

## A Common Misconception

Many developers think performance engineering is about making code faster.

Often,

it's about making code **do less**.

Compose doesn't try to execute every operation more quickly.

It constantly asks:

> **Can this operation be skipped entirely?**

Skipping unnecessary work is almost always more valuable than making unnecessary work slightly faster.

---

## Bringing Everything Together

```text
    Need To Produce UI
          ↓
  Can The Runtime
  Avoid This Work?
            │
     ┌──────┴──────┐
     │             │
    Yes           No
     │             │
     ▼             ▼
Skip Work     Perform Work
                    │
                    ▼
          Produce Correct UI
```

Notice something.

The runtime never sacrifices correctness.

Optimization only happens when correctness can still be guaranteed.

---

## Completing Part 6 — Compose Runtime Optimization

Throughout this part,

we've learned how the Compose Runtime thinks.

It doesn't try to make every operation faster.

Instead,

it continually asks:

> **Can I safely avoid doing this work?**

Sometimes that means:

- skipping recomposition,
- trusting immutable models,
- preserving identity,
- delaying UI creation.

Different optimizations.

One philosophy.

Understanding that philosophy is far more valuable than memorizing individual APIs.

---

## Revision

### Core Idea

Most Compose performance problems aren't caused by missing APIs.

They're caused by patterns that force the runtime to perform unnecessary work.

---

### Engineering Mindset

Don't think:

> **"Which optimization should I use?"**

Think:

> **"What unnecessary work is happening?"**

Once you identify the unnecessary work,

the appropriate optimization often becomes obvious.

---

### Key Insight

Compose performance isn't about making everything faster.

It's about avoiding work that never needed to happen in the first place.

---

## One Remaining Question

We've now learned how the Compose Runtime thinks.

A natural question appears.

> **How do experienced teams organize hundreds of composables, reusable components, and design systems into a maintainable application?**

That begins the final part of this document.

**Compose Architecture Patterns**
