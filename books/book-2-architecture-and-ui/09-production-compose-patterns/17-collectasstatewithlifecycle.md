# Lifecycle-Aware State Collection (`collectAsStateWithLifecycle`)

## Observation

Earlier in the Atlas,

we learned how to display a `StateFlow` inside Compose.

```kotlin
val uiState by
    viewModel.uiState.collectAsState()
```

Everything worked beautifully.

Whenever the ViewModel emitted a new state,

Compose automatically recomposed.

At this point,

it feels like the problem is solved.

So why did Google introduce another API?

```kotlin
collectAsStateWithLifecycle()
```

If `collectAsState()` already works,

what problem is this new API solving?

---

## A Production Example

Imagine a screen displaying stock prices.

```kotlin
val prices by
    viewModel.stockPrices.collectAsState()
```

Every second,

the ViewModel emits new prices.

The UI updates automatically.

Everything looks perfect.

Now imagine the user presses the Home button.

The application moves to the background.

A natural question appears.

> **Should this screen continue collecting stock prices even though nobody can see it?**

---

## The First Thought

Our first instinct might be:

> **Why not?**

The collection is already running.

The ViewModel is still alive.

Nothing seems wrong.

But let's think a little deeper.

---

## What's Actually Happening?

While the app is in the background,

the Flow may still be producing values.

Those values may trigger:

- database reads,
- network requests,
- expensive Flow operators,
- object allocations,
- recompositions.

Yet...

the screen isn't visible.

We're doing work for a UI that doesn't currently exist on the screen.

---

## The Real Problem

Notice something.

The coroutine collecting the Flow is following the composable's lifetime.

But the user interacts with something different.

The Android lifecycle.

Earlier in the Atlas,

we learned that an Activity can move between states.

```text
Visible

↓

Background

↓

Visible Again
```

Those transitions matter.

A screen that's in the background shouldn't continue behaving exactly like a visible screen.

This is another lifecycle mismatch.

---

## The Lifecycle We Actually Want

What we really want is this.

```text
Screen Becomes Visible

↓

Start Collecting

↓

Screen Goes To Background

↓

Pause Collection

↓

Screen Becomes Visible Again

↓

Resume Collection
```

Notice something important.

We're not destroying the screen.

We're not destroying the ViewModel.

We're simply saying:

> **Only collect while the screen is active enough to display updates.**

---

## Introducing `collectAsStateWithLifecycle`

This is exactly what the API provides.

```kotlin
val uiState by
    viewModel.uiState
        .collectAsStateWithLifecycle()
```

Now the collection automatically follows the Android lifecycle.

When the screen becomes inactive,

collection pauses.

When the screen becomes active again,

collection resumes.

The UI performs work only when that work is actually useful.

---

## Why Not Just Use `collectAsState()`?

A natural question appears.

> **Does this mean `collectAsState()` is wrong?**

Not at all.

`collectAsState()` does exactly what it promises.

It converts a Flow into Compose state.

It simply doesn't know anything about the Android lifecycle.

`collectAsStateWithLifecycle()` builds on top of it by adding another concern.

Lifecycle awareness.

---

## Connecting Earlier Documents

Notice how many ideas come together here.

From **Doc 3**,

we learned how to collect Flows.

```text
Flow

↓

collect()
```

From **Doc 5**,

we learned that screens have lifecycles.

```text
Created

↓

Started

↓

Resumed

↓

Paused

↓

Stopped
```

From **Doc 6**,

we learned how Compose observes a Flow.

```text
StateFlow

↓

collectAsState()

↓

Compose UI
```

This chapter simply connects those ideas.

```text
Flow

↓

Android Lifecycle

↓

Compose UI
```

Rather than collecting forever,

the collection now respects the screen's lifecycle.

---

## Another Production Example

Imagine a dashboard displaying live delivery updates.

```kotlin
val orders by
    viewModel.orders
        .collectAsStateWithLifecycle()
```

When the user switches to another application,

the dashboard is no longer visible.

There's little value in continuously updating UI state that nobody can currently see.

When the user returns,

collection resumes automatically,

and the latest state is displayed.

The UI behaves exactly as users expect.

---

## Thinking Like A Production Engineer

Whenever a screen observes a Flow,

ask yourself:

> **Should this screen continue doing work when it isn't visible?**

If the answer is:

> **No**

the collection should follow the Android lifecycle.

---

## A Common Misconception

Some developers think:

> **"`collectAsStateWithLifecycle()` makes Flow lifecycle-aware."**

It doesn't.

The Flow itself doesn't change.

Only the UI's collection changes.

The producer can continue existing.

The ViewModel can continue existing.

The difference is simply that the UI pauses observation while it isn't active.

That's an important distinction.

---

## Comparing The Two APIs

| Situation | API |
|-----------|-----|
| Convert a Flow into Compose state | `collectAsState()` |
| Convert a Flow into Compose state while respecting the Android lifecycle | `collectAsStateWithLifecycle()` |

The second API doesn't replace the first.

It extends it by integrating another lifecycle.

---

## Bringing Everything Together

Notice the progression we've built across the Atlas.

```text
Flow

↓

collect()

↓

collectAsState()

↓

collectAsStateWithLifecycle()

──────────────────────────

What Changed?

──────────────────────────

Flow

↓

Compose

↓

Compose + Android Lifecycle
```

Each step adds another layer of responsibility.

Nothing is replaced.

Each API builds upon the previous one.

---

## Which API Should I Use?

After learning both APIs,

a natural question appears.

> **As an Android developer, should I use `collectAsState()` or `collectAsStateWithLifecycle()`?**

For most modern Android applications,

the answer is simple.

Your default choice should usually be:

```kotlin
val uiState by
    viewModel.uiState.collectAsStateWithLifecycle()
```

because it automatically pauses and resumes Flow collection based on the Android lifecycle.

So why does `collectAsState()` still exist?

The reason is that Compose itself isn't Android-specific.

The Compose Runtime also powers:

- Compose Desktop
- Compose Multiplatform
- other Compose environments that don't have Android's `Lifecycle`.

Because of that,

the Compose Runtime provides the platform-independent API:

```kotlin
collectAsState()
```

Android then builds on top of it by adding lifecycle awareness.

```text
Compose Runtime

↓

collectAsState()

────────────────────

Android Integration

↓

collectAsStateWithLifecycle()
```

As an Android application developer,

you'll use `collectAsStateWithLifecycle()` far more often.

`collectAsState()` is still an important API because it provides the fundamental bridge from Flow to Compose state,

while `collectAsStateWithLifecycle()` extends that bridge to respect the Android lifecycle.

---

## Revision

### Core Idea

`collectAsStateWithLifecycle()` converts a Flow into Compose state while automatically pausing and resuming collection based on the Android lifecycle.

---

### Engineering Mindset

Don't ask:

> **"Can I collect this Flow?"**

Ask:

> **"Should this UI continue collecting while it isn't visible?"**

If the answer is no,

the collection should be lifecycle-aware.

---

### Key Insight

`collectAsState()` understands Compose.

`collectAsStateWithLifecycle()` understands both Compose **and** the Android lifecycle.

That small difference prevents unnecessary work when the UI isn't actively being displayed.

---

> **You'll see this in...**
> - **DOC 11 — Remote Communication**, where the Flow from a Repository is collected lifecycle-aware so network polling pauses when the screen is in the background
> - **DOC 17 — Performance & Optimization**, where using `collectAsState()` instead of `collectAsStateWithLifecycle()` is a common performance mistake — it keeps upstream Flows active even when the UI isn't visible

---

## One Remaining Question

We've learned how Compose automatically pauses Flow collection when observing state.

A natural question appears.

> **What if we aren't inside Compose at all?**

Suppose we're collecting a Flow from an Activity,

a Fragment,

or another lifecycle-aware component.

How can we pause and resume collection there?

That leads us to the next concept.

**`repeatOnLifecycle`**
