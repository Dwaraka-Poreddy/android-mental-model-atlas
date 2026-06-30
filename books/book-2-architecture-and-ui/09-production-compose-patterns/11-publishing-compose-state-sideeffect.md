# Publishing Committed Compose State (`SideEffect`)

## Observation

So far,

we've learned about two kinds of side effects.

- `LaunchedEffect` manages coroutine lifecycles.
- `DisposableEffect` manages setup and cleanup lifecycles.

A natural question appears.

> **What if I don't need a coroutine or a resource?**

Suppose I simply want to notify some external object about the latest Compose state.

For example:

- updating an analytics SDK,
- synchronizing a legacy View,
- informing a logging framework,
- updating a non-Compose object.

Those operations don't need coroutines.

They don't need cleanup.

They simply need to happen **after Compose has successfully committed the latest UI.**

That distinction is far more important than it first appears.

---

## A First Attempt

Imagine our application supports dark mode.

Compose already knows the current theme.

```kotlin
val isDarkTheme = ...
```

Now suppose an analytics SDK also wants to know.

A first attempt might look like this.

```kotlin
@Composable
fun HomeScreen() {

    analytics.setDarkTheme(isDarkTheme)

    ...

}
```

At first glance,

this seems perfectly reasonable.

The analytics SDK receives the latest value every time the UI updates.

So why isn't this recommended?

---

## The Missing Detail

Remember something important from previous chapters.

Composable functions execute while Compose is **building** the next UI.

At that point,

the new UI hasn't been committed yet.

Compose is still calculating.

That means the composition could still fail,

or even be abandoned.

Imagine this sequence.

```text
Compose Starts Building UI

↓

analytics.setDarkTheme()

↓

Continue Building UI

↓

Unexpected Exception

↓

Composition Aborted
```

Question.

Did the UI actually change?

No.

The composition never completed.

But the analytics SDK has already been updated.

The outside world now believes something happened that the user never actually saw.

Your UI and your external systems have become inconsistent.

---

## The Lifecycle We Actually Want

What we really want is this.

```text
Compose Builds UI

↓

Composition Succeeds

↓

UI Is Committed

↓

Now Publish
The Latest State
```

Notice the difference.

We're not publishing the state that Compose is *trying* to build.

We're publishing the state that Compose has **successfully committed**.

That's a very important guarantee.

---

## Introducing `SideEffect`

This is exactly what `SideEffect` provides.

```kotlin
@Composable
fun HomeScreen() {

    SideEffect {

        analytics.setDarkTheme(isDarkTheme)

    }

    ...

}
```

Now the analytics SDK is updated only after Compose has successfully committed the latest UI.

The outside world always stays synchronized with what the user actually sees.

---

## Another Production Example

Imagine a custom map SDK.

Compose calculates the latest zoom level.

```kotlin
val zoom = ...
```

The map should always reflect the current Compose state.

Without `SideEffect`,

you might write:

```kotlin
map.setZoomLevel(zoom)
```

If composition later fails,

the map changes,

but the Compose UI does not.

The two are now out of sync.

Using `SideEffect` instead:

```kotlin
SideEffect {

    map.setZoomLevel(zoom)

}
```

guarantees that the map updates only after the new UI has been successfully committed.

Both systems remain consistent.

---

## The Engineering Principle

`SideEffect` isn't about launching work.

It isn't about managing resources.

It isn't even about recomposition.

It's about one guarantee.

> **Only publish state that Compose has successfully committed.**

That's why it exists.

---

## Thinking Like A Production Engineer

Whenever you need to update something outside Compose,

ask yourself two questions.

### Question 1

Should this happen after every successful UI update?

If the answer is:

> **No**

then another Side Effect API is probably more appropriate.

---

### Question 2

Should the outside world observe only committed UI state?

If the answer is:

> **Yes**

then `SideEffect` is usually the correct solution.

---

## Comparing The Side Effect APIs

Notice how each API solves a different lifecycle problem.

| Engineering Problem | API |
|----------------------|-----|
| Launch a coroutine tied to the composable lifecycle | `LaunchedEffect` |
| Acquire and later release a resource | `DisposableEffect` |
| Publish committed Compose state to the outside world | `SideEffect` |

None of these APIs overlap.

Each one exists because it provides a different lifecycle guarantee.

---

## A Common Misconception

Many developers think:

> **"`SideEffect` just runs after every recomposition."**

Technically,

that's true.

But it misses the most important part.

The real guarantee is:

> **It runs only after a successful composition has been committed.**

That means external systems never observe UI that Compose failed to display.

This guarantee is the entire reason the API exists.

---

## Bringing Everything Together

The Side Effects section now forms a complete story.

```text
Why Side Effects Exist

↓

External Work Must Follow
The Correct Lifecycle

────────────────────────

LaunchedEffect

↓

Coroutine Lifecycle

────────────────────────

Keys & Identity

↓

Runtime Identity

────────────────────────

DisposableEffect

↓

Setup & Cleanup

────────────────────────

SideEffect

↓

Publish Only
Committed UI State
```

Compose isn't asking us:

> "Which API would you like to use?"

Instead,

each API answers a much better question.

> **What lifecycle guarantee does this work require?**

---

## Revision

### Core Idea

`SideEffect` publishes Compose state to external systems,

but only after Compose has successfully committed the latest UI.

This keeps external systems synchronized with what the user actually sees.

### Engineering Mindset

Don't ask:

> **"Can I update this object from a composable?"**

Ask:

> **"Should this object observe only committed Compose state?"**

If the answer is yes,

`SideEffect` is usually the correct solution.

### Key Insight

The value of `SideEffect` isn't that it runs after recomposition.

The value is that it runs **only after a successful composition has been committed**, ensuring external systems never observe UI state that the user never actually sees.

---

## One Remaining Question

So far,

every side effect we've discussed starts automatically as part of the composable lifecycle.

A natural question appears.

> **What if I need to launch a coroutine only when the user performs an action, like clicking a button, instead of automatically when the composable enters the composition?**

That leads us to the next concept.

**Event-Driven Coroutines (`rememberCoroutineScope`)**
