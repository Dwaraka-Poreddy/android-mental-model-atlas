# Lifecycle-Aware Flow Collection (`repeatOnLifecycle`)

## Observation

In the previous chapter,

we learned:

```kotlin
val uiState by
    viewModel.uiState
        .collectAsStateWithLifecycle()
```

Compose automatically paused and resumed Flow collection based on the Android lifecycle.

Everything worked beautifully.

A natural question now appears.

> **What if we aren't inside Compose?**

Suppose we're inside:

- an Activity,
- a Fragment,
- or another lifecycle-aware Android component.

We still have:

- a Flow,
- an Android lifecycle.

Shouldn't we still pause collection when the UI is no longer visible?

---

## A Production Example

Imagine we're inside an Activity.

```kotlin
class MainActivity : AppCompatActivity() {

    override fun onCreate(...) {

        lifecycleScope.launch {

            viewModel.uiState.collect {

                render(it)

            }

        }

    }

}
```

At first,

this looks perfectly fine.

The Flow emits values.

The Activity renders them.

Everything works.

Now imagine the user presses the Home button.

The Activity moves to the background.

A natural question appears.

> **Does the collection stop?**

No.

The coroutine is still running.

The Flow is still being collected.

We're once again doing work for a screen the user cannot currently see.

---

## The Problem Isn't New

Notice something interesting.

This is exactly the same engineering problem we solved in the previous chapter.

The only thing that changed is the environment.

Previously,

we had:

```text
Compose

↓

collectAsStateWithLifecycle()
```

Now we have:

```text
Activity / Fragment

↓

?
```

The engineering problem is identical.

Only the API changes.

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

Stop Collecting

↓

Screen Becomes Visible Again

↓

Start Collecting Again
```

Notice something.

The work doesn't stop forever.

It simply follows the lifecycle of the screen.

---

## Introducing `repeatOnLifecycle`

Android provides exactly this abstraction.

```kotlin
lifecycleScope.launch {

    repeatOnLifecycle(
        Lifecycle.State.STARTED
    ) {

        viewModel.uiState.collect {

            render(it)

        }

    }

}
```

Now the Flow is only collected while the lifecycle is at least in the `STARTED` state.

When the Activity moves to the background,

the collection automatically stops.

When the Activity becomes visible again,

the collection automatically starts again.

Exactly the behavior we wanted.

---

## Why Doesn't `DisposableEffect` Solve This?

After learning `DisposableEffect`,

a natural question appears.

> **Why can't we simply launch the coroutine inside `DisposableEffect` and cancel it in `onDispose()`?**

That would certainly cancel the collection.

The problem is **when** `onDispose()` executes.

`DisposableEffect` belongs to the **Compose lifecycle**.

`onDispose()` only runs when the composable leaves the composition.

Our problem is different.

The composable can still exist while the Activity has moved to the background.

```text
Composable

↓

Still In Composition

────────────────────────

Activity

↓

onStop()
```

The composable hasn't disappeared.

So `onDispose()` doesn't execute.

Yet the screen is no longer visible.

The collection continues.

This is exactly the gap that lifecycle-aware collection solves.

It's not about the Compose lifecycle.

It's about the Android lifecycle.

---

## Couldn't I Manage This Manually?

Suppose we write:

```kotlin
override fun onStart() {

    super.onStart()

    job = lifecycleScope.launch {

        viewModel.uiState.collect {

            render(it)

        }

    }

}

override fun onStop() {

    super.onStop()

    job.cancel()

}
```

Would this work?

Yes.

There's nothing fundamentally wrong with it.

So why does `repeatOnLifecycle()` exist?

Because this lifecycle pattern appears repeatedly throughout Android applications.

Instead of manually remembering to:

- launch,
- cancel,
- relaunch,
- cancel again,

Android packages that recurring lifecycle pattern into a reusable abstraction.

Just like `produceState`,

it doesn't introduce new capabilities.

It simply removes repetitive lifecycle management.

---

## Why Is It Called `repeatOnLifecycle`?

At first,

the name sounds a little strange.

> **Repeat what?**

Imagine this timeline.

```text
STARTED

↓

Begin Collecting

↓

STOPPED

↓

Cancel Collection

↓

STARTED Again

↓

Begin Collecting Again

↓

STOPPED

↓

Cancel Again

↓

STARTED Again

↓

Begin Collecting Again
```

Notice what happens.

Every time the lifecycle reaches the requested state,

the block executes again.

That's why it's called:

> **`repeatOnLifecycle`**

It repeatedly starts and stops the work as the lifecycle changes.

---

## Connecting Earlier Documents

Notice how many concepts come together here.

From **Doc 3**,

we learned how to collect a Flow.

```text
Flow

↓

collect()
```

From **Doc 5**,

we learned about the Android lifecycle.

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

↓

Destroyed
```

From the previous chapter,

we learned:

```text
Flow

↓

collectAsStateWithLifecycle()

↓

Compose
```

This chapter simply solves the same lifecycle problem outside Compose.

---

## Comparing The Two APIs

| Environment | API |
|-------------|-----|
| Compose | `collectAsStateWithLifecycle()` |
| Activity / Fragment | `repeatOnLifecycle()` |

They're solving the same engineering problem.

They simply operate in different environments.

---

## Thinking Like A Production Engineer

Whenever you're collecting a Flow,

ask yourself:

> **Who is responsible for deciding when this collection should be active?**

If the collector belongs to:

- a Compose UI,

use `collectAsStateWithLifecycle()`.

If the collector belongs to:

- an Activity,
- a Fragment,
- or another lifecycle-aware Android component,

use `repeatOnLifecycle()`.

---

## A Common Misconception

Some developers think:

> **"`repeatOnLifecycle()` makes the Flow lifecycle-aware."**

It doesn't.

The Flow itself doesn't change.

The producer doesn't change.

Only the **collector** becomes lifecycle-aware.

That's an important distinction.

---

## A Bigger Engineering Lesson

Notice something interesting.

We've now encountered this pattern several times throughout the Atlas.

Earlier,

we learned:

- `produceState`
- `collectAsStateWithLifecycle`

Now we've learned:

- `repeatOnLifecycle`

None of these APIs introduce brand-new capabilities.

Everything they do could be implemented manually.

Instead,

they package recurring engineering patterns into reusable abstractions.

That's an important lesson.

Experienced engineers don't create abstractions because something is impossible.

They create abstractions because the same solution keeps appearing repeatedly.

The abstraction reduces boilerplate,

makes intent clearer,

and helps prevent subtle lifecycle bugs.

---

## Bringing Everything Together

The lifecycle-aware collection story is now complete.

```text
Need To Collect A Flow

                      │
                      │
     ┌────────────────┴─────────────────┐

     │                                  │

     ▼                                  ▼

Compose UI                     Activity / Fragment

     │                                   │

     ▼                                   ▼

collectAsStateWithLifecycle()  repeatOnLifecycle()
```

Both APIs answer the same engineering question.

They simply apply it in different environments.

---

## Revision

### Core Idea

`repeatOnLifecycle()` repeatedly starts and stops a block of code as the Android lifecycle enters and leaves a specified state.

It allows Flow collection to automatically follow the lifecycle of an Activity or Fragment.

---

### Engineering Mindset

Don't ask:

> **"How do I start collecting this Flow?"**

Ask:

> **"Who should decide when this Flow is allowed to be collected?"**

If the answer is the Android lifecycle,

`repeatOnLifecycle()` is usually the correct abstraction.

---

### Key Insight

`repeatOnLifecycle()` doesn't make Flows lifecycle-aware.

It makes **Flow collection** lifecycle-aware.

That's a subtle but important distinction.

---

## One Remaining Question

We've now learned how to make Flow collection lifecycle-aware.

A natural question appears.

> **What if lifecycle awareness isn't enough?**

What if we want to collect data **only while the UI is actively visible**, avoid unnecessary work, and follow best practices consistently throughout the application?

That leads us to the final concept in this part.

**Lifecycle-Aware Collection Patterns**
