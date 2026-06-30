# Long-Lived Effects With Latest State (`rememberUpdatedState`)

## Observation

Throughout this section,

we've learned several ways to control the lifecycle of work.

- `LaunchedEffect` starts work when a composable enters the composition.
- `DisposableEffect` manages setup and cleanup.
- `SideEffect` publishes committed Compose state.
- `rememberCoroutineScope` lets events launch coroutines.

At this point,

it feels like we've covered every lifecycle.

But one subtle problem still remains.

Imagine you've already started a long-running coroutine.

Now one of the values used inside that coroutine changes because the UI recomposed.

A natural question appears.

> **Should the coroutine restart?**

Sometimes,

the answer is **yes**.

But surprisingly,

sometimes the answer is **absolutely not**.

---

## A Simple Example

Suppose we write:

```kotlin
LaunchedEffect(Unit) {

    while (true) {

        delay(1000)

        println(message)

    }

}
```

When does this coroutine start?

You already know.

When the composable enters the composition.

Perfect.

Now imagine the initial UI state is:

```text
message = "Loading..."
```

The coroutine prints:

```text
Loading...

Loading...

Loading...
```

Everything works.

---

## Then The UI Changes

A few seconds later,

the ViewModel emits new UI state.

```text
message = "Download Complete"
```

The composable recomposes.

Now pause for a moment.

What should happen?

---

## The Obvious Solution

One possibility is:

```text
Cancel Old Coroutine

↓

Start New Coroutine

↓

Print

Download Complete
```

That certainly gives us the latest value.

But...

should we always restart the coroutine?

Let's think about it.

---

## A Better Example

Imagine a countdown timer.

```kotlin
LaunchedEffect(Unit) {

    repeat(60) {

        delay(1000)

        println(it)

    }

}
```

Halfway through,

the user changes the app theme.

Should the timer restart from 60?

Of course not.

Changing the theme has nothing to do with the timer.

Restarting would actually introduce a bug.

The timer should continue exactly where it was.

---

## Another Production Example

Suppose we're collecting location updates.

```kotlin
LaunchedEffect(Unit) {

    locationFlow.collect {

        ...

    }

}
```

While the collection is running,

the user edits the screen title.

Should we:

- cancel the collection,
- disconnect from GPS,
- reconnect,
- start collecting again?

Again,

no.

The collection should continue uninterrupted.

---

## So We Have A Conflict

We now have two requirements.

Requirement 1:

```text
The Coroutine
Must Continue Running
```

Requirement 2:

```text
The Coroutine
Must See
The Latest Values
```

At first,

those seem impossible to satisfy together.

Normally,

a coroutine captures the values that existed when it started.

The easiest way to observe new values is to restart it.

But we've already decided restarting would be wrong.

So...

how can the coroutine see new values without restarting?

---

## Understanding The Problem

Imagine the coroutine is reading from a notebook.

When it first starts,

the notebook contains:

```text
Loading...
```

Every second,

the coroutine opens the notebook and reads the text.

Now the UI changes.

If nobody updates the notebook,

the coroutine will continue reading:

```text
Loading...
```

forever.

Restarting the coroutine would give it a brand-new notebook.

But we don't actually want a new coroutine.

We only want the notebook to contain the latest value.

That is exactly the problem `rememberUpdatedState` solves.

---

## Introducing `rememberUpdatedState`

```kotlin
val latestMessage by
    rememberUpdatedState(message)
```

Instead of capturing `message` directly,

the coroutine reads `latestMessage`.

```kotlin
val latestMessage by
    rememberUpdatedState(message)

LaunchedEffect(Unit) {

    while (true) {

        delay(1000)

        println(latestMessage)

    }

}
```

Notice what changed.

The coroutine never restarted.

Yet every iteration prints the newest value.

Exactly the behavior we wanted.

---

## What's Actually Happening?

Don't think of `rememberUpdatedState()` as restarting anything.

Instead,

think of it as a small container.

Every recomposition,

Compose quietly updates that container.

```text
Recomposition

↓

Update Container

────────────────────

Running Coroutine

↓

Read Container

↓

Always Gets

Latest Value
```

The coroutine stays alive.

Only the value inside the container changes.

---

## Connecting It Back To Keys

Earlier,

we learned about Keys & Identity.

Changing a key means:

```text
Identity Changed

↓

Cancel Old Effect

↓

Start New Effect
```

That makes perfect sense when the work itself should restart.

But sometimes,

the identity hasn't changed.

The work is still valid.

Only the data has changed.

In those situations,

restarting the effect would be unnecessary.

Instead,

we keep the effect alive and update the values it observes.

This is why `rememberUpdatedState` exists.

Notice how beautifully it complements the Keys chapter.

Keys answer:

> **Should the effect restart?**

`rememberUpdatedState` answers:

> **If it shouldn't restart, how does it still observe the latest values?**

Together,

they solve both halves of the problem.

---

## A Real-World Example

Imagine a timer that waits five seconds before showing a snackbar.

```kotlin
LaunchedEffect(Unit) {

    delay(5000)

    onTimeout()

}
```

Initially,

`onTimeout` points to one callback.

Before five seconds pass,

the parent composable recomposes and provides a different callback.

Question.

Should we restart the five-second timer?

No.

The timer is already halfway finished.

Restarting it would make the user wait another five seconds.

But when the timer finishes,

we still want to invoke the **latest** callback.

That's exactly the situation where `rememberUpdatedState` shines.

```kotlin
val latestOnTimeout by
    rememberUpdatedState(onTimeout)

LaunchedEffect(Unit) {

    delay(5000)

    latestOnTimeout()

}
```

The timer continues uninterrupted,

yet still invokes the newest callback.

This is one of the most common real-world uses of the API.

---

## Thinking Like A Production Engineer

Whenever you're writing a long-running effect,

ask yourself two questions.

### Question 1

Should this effect restart when this value changes?

If the answer is:

> **Yes**

that value probably belongs in the effect's key.

---

### Question 2

Should the effect continue running,

but simply observe the latest value?

If the answer is:

> **Yes**

`rememberUpdatedState` is usually the right solution.

---

## Comparing The Two Approaches

| Situation | Solution |
|-----------|----------|
| The work itself should restart | Change the key |
| The work should continue but observe new values | `rememberUpdatedState` |

This is one of the most important distinctions in the entire Side Effects section.

---

## A Common Misconception

Some developers think:

> **"`rememberUpdatedState` avoids recomposition."**

It doesn't.

Recomposition still happens normally.

The composable still receives the newest state.

The only thing that doesn't happen is:

> **The running effect isn't restarted.**

Those are completely different ideas.

---

## Bringing Everything Together

The entire Side Effects section now forms a complete mental model.

```text
Need External Work

↓

Who Owns
The Lifecycle?

────────────────────────

Compose Starts Work

↓

LaunchedEffect

────────────────────────

User Starts Work

↓

rememberCoroutineScope

────────────────────────

Setup & Cleanup

↓

DisposableEffect

────────────────────────

Publish Committed UI

↓

SideEffect

────────────────────────

Should Work Restart?

↓

Yes

↓

Change The Key

────────────────────────

No

↓

rememberUpdatedState
```

Notice how every API answers a different engineering question.

Nothing overlaps.

Everything fits together.

---

## Revision

### Core Idea

`rememberUpdatedState` allows a long-running effect to observe the latest values from recomposition without restarting the effect itself.

### Engineering Mindset

Don't ask:

> **"How do I get the latest value?"**

Ask two questions:

1. Should the effect restart?
2. Or should the effect continue and simply observe newer values?

The answer determines whether you should change the key or use `rememberUpdatedState`.

### Key Insight

Keys and `rememberUpdatedState` solve complementary problems.

- Keys decide **whether work should restart**.
- `rememberUpdatedState` decides **how running work observes new values without restarting**.

---

## Completing Part 3 — Side Effects

Congratulations.

You've now learned every major Side Effect API in Compose.

More importantly,

you've learned something much deeper than a collection of APIs.

You've learned to reason about **lifecycles**.

Every Side Effect API exists because different kinds of work belong to different lifecycles.

Once you identify the lifecycle that should own the work,

the correct API usually becomes obvious.

This mindset is far more valuable than memorizing APIs,

and it will continue to guide you as you learn more advanced Compose concepts.

---

## One Remaining Question

So far,

we've focused on **how work happens around state**.

A natural next question appears.

> **How can we transform, derive, or bridge state itself without introducing unnecessary recompositions or boilerplate?**

That begins the next part of the Atlas.

**State Helpers**
