# Choosing The Right Lifecycle-Aware Collection Strategy

## Observation

Over the past few chapters,

we've learned several different ways to collect a Flow.

```kotlin
collect()
```

```kotlin
collectAsState()
```

```kotlin
collectAsStateWithLifecycle()
```

```kotlin
repeatOnLifecycle()
```

At first,

this feels a little overwhelming.

A natural question appears.

> **When should I use each one?**

Instead of memorizing APIs,

let's step back and look at the bigger picture.

---

## The Real Question

Notice something.

Every API we've learned solves the same problem.

> **Collect a Flow.**

The difference isn't **what** they do.

The difference is **where the collector lives.**

Once you answer that question,

choosing the correct API becomes almost automatic.

---

## Case 1 — Business Logic

Suppose you're inside:

- a Repository,
- a Use Case,
- a ViewModel,
- or another coroutine that isn't tied to a UI.

You're simply consuming a Flow.

```kotlin
viewModelScope.launch {

    repository.users.collect {

        ...

    }

}
```

There's no Android lifecycle involved.

There's no Compose involved.

You simply use:

```kotlin
collect()
```

Nothing more is needed.

---

## Case 2 — Compose UI

Now suppose you're displaying state on the screen.

```kotlin
val uiState by
    viewModel.uiState
        .collectAsStateWithLifecycle()
```

The collector now belongs to the Compose UI.

The screen should stop collecting when it isn't visible.

Compose already provides the correct abstraction.

Use:

```kotlin
collectAsStateWithLifecycle()
```

---

## Case 3 — Activity Or Fragment

Suppose you're collecting a Flow directly inside an Activity or Fragment.

```kotlin
lifecycleScope.launch {

    repeatOnLifecycle(
        Lifecycle.State.STARTED
    ) {

        viewModel.events.collect {

            ...

        }

    }

}
```

Here,

the collector belongs to the Android lifecycle.

Compose isn't involved.

Use:

```kotlin
repeatOnLifecycle()
```

---

## A Simple Decision Framework

Whenever you need to collect a Flow,

ask one question.

> **Where does the collector live?**

The answer determines the API.

```text
                               Need To Collect A Flow
                                         ↓
                                Who Owns The Collector?
                                          │
       ┌──────────────────────────────────┼────────────────────────────────┐
       │                                  │                                │
       ▼                                  ▼                                ▼
   Business Logic                    Compose UI                    Activity / Fragment
       │                                  │                                │
       ▼                                  ▼                                ▼
    collect()               collectAsStateWithLifecycle()            repeatOnLifecycle()
```

Notice something.

You don't start by choosing an API.

You start by identifying the owner.

The API naturally follows.

---

## Common Mistake #1

Using:

```kotlin
collectAsState()
```

inside a normal Android screen.

While this works,

the collection continues regardless of the Android lifecycle.

For most Android applications,

the preferred choice is:

```kotlin
collectAsStateWithLifecycle()
```

because it pauses collection when the screen is no longer active.

---

## Common Mistake #2

Collecting directly inside:

```kotlin
lifecycleScope.launch {

    flow.collect {

        ...

    }

}
```

without:

```kotlin
repeatOnLifecycle()
```

The coroutine continues collecting even after the screen moves to the background.

The work keeps happening,

but the user can no longer see the results.

---

## Common Mistake #3

Treating Every Collector The Same

Many developers memorize APIs.

Experienced engineers think differently.

They ask:

> **Who owns this collector?**

The owner determines:

- the lifetime,
- the lifecycle,
- and therefore the correct abstraction.

Thinking this way makes API selection almost effortless.

---

## Connecting The Atlas

Notice how many documents come together here.

From **Doc 3**,

we learned:

```text
Flow
```

From **Doc 5**,

we learned:

```text
Android Lifecycle
```

From **Doc 6**,

we learned:

```text
Compose
```

From **Part 5**,

we learned how these systems cooperate.

Instead of treating them as separate technologies,

we now understand how they fit together.

---

## Thinking Like A Production Engineer

When you encounter a new API,

don't immediately ask:

> **"How do I use it?"**

Instead ask:

> **"What problem is it solving?"**

In this case,

every API collects a Flow.

The real difference is:

**who owns the collection.**

Once you identify the owner,

the correct API becomes obvious.

---

## Bringing Everything Together

The entire lifecycle-aware collection story can now be summarized in one diagram.

```text
                               Need To Collect A Flow
                                         ↓
                                Who Owns The Collector?
                                          │
       ┌──────────────────────────────────┼────────────────────────────────┐
       │                                  │                                │
       ▼                                  ▼                                ▼
   Business Logic                    Compose UI                    Activity / Fragment
       │                                  │                                │
       ▼                                  ▼                                ▼
    collect()               collectAsStateWithLifecycle()            repeatOnLifecycle()
```

Rather than memorizing three APIs,

remember one principle.

> **The owner determines the collector.**

---

## Revision

### Core Idea

All Flow collection APIs perform the same fundamental job.

The correct API depends on **who owns the collector** and **which lifecycle should control it.**

---

### Engineering Mindset

Don't ask:

> **"Which Flow API should I use?"**

Ask:

> **"Where does this collector live?"**

The answer naturally determines the correct abstraction.

---

### Key Insight

Experienced engineers don't choose APIs first.

They identify ownership first.

The API is simply a consequence of that decision.

---

## Completing Part 5 — Lifecycle Integration

Congratulations.

You've now learned how Flow,

Compose,

and the Android lifecycle cooperate.

- Sometimes a Flow belongs to business logic.
- Sometimes it feeds a Compose UI.
- Sometimes it's collected directly by an Activity or Fragment.

Although the APIs are different,

the engineering principle remains the same.

> **A collector should only do work while its owner is responsible for that work.**

Once you understand ownership,

the correct lifecycle and the correct API become obvious.

---

## One Remaining Question

So far,

we've learned how to write correct Compose code.

A natural question now appears.

> **How do experienced teams keep large Compose codebases maintainable as applications grow from a few screens to hundreds?**

That begins the final part of this document.

**Scaling Compose Applications**
