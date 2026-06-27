# Converting Compose State Into Flow (`snapshotFlow`)

## Observation

Throughout this part,

we've learned how state moves through an application.

First,

we learned how to derive new Compose state from existing Compose state.

Then,

we learned how to bring external data into Compose.

A natural question now appears.

> **What if we need to go in the opposite direction?**

Suppose our data already exists as Compose state,

but another part of the application expects a `Flow`.

How do we bridge those two worlds?

---

## A Production Example

Imagine a search screen.

```kotlin
var query by remember {

    mutableStateOf("")

}
```

As the user types,

the value changes.

```text
H

He

Hel

Hell

Hello
```

Now suppose we don't want to search immediately.

Instead,

we want to:

- wait until the user stops typing,
- ignore duplicate queries,
- cancel previous searches.

If you've read the Flow section of the Atlas,

this should immediately remind you of something.

```kotlin
debounce()

distinctUntilChanged()

collectLatest()
```

Flow already solves this problem beautifully.

There's only one issue.

`query` isn't a `Flow`.

It's Compose state.

---

## The Real Problem

Compose state is designed for recomposition.

Flow is designed for asynchronous pipelines.

Those are different worlds.

We can't simply write:

```kotlin
query

    .debounce(300)

    .collect { ... }
```

because `query` isn't a Flow.

We now have another bridge problem.

Earlier,

we asked:

> **How do we convert external data into Compose state?**

Now we're asking:

> **How do we convert Compose state into a Flow?**

---

## The Obvious Solution

Our first instinct might be:

> **"I'll manually create a Flow and emit values whenever the state changes."**

But then several questions immediately appear.

Who observes the Compose state?

Who decides when to emit?

How do we correctly participate in Compose's Snapshot System?

How do we avoid missing updates?

Suddenly,

there's much more happening than simply calling `emit()`.

---

## Looking For A Pattern

Notice the recurring lifecycle.

```text
Compose State

↓

Observe Changes

↓

Emit Values

↓

Flow Pipeline
```

Just like `produceState`,

this pattern appears repeatedly.

Whenever Compose state needs to participate in coroutine pipelines,

we end up writing the same bridge.

---

## Introducing `snapshotFlow`

Compose already provides that bridge.

```kotlin
snapshotFlow {

    query

}
```

`snapshotFlow` observes the Compose state read inside its block.

Whenever that state changes,

it emits the latest value as a Flow.

The result is a normal Flow,

which means every Flow operator you've already learned becomes available.

---

## A Production Example

Our search pipeline now becomes:

```kotlin
LaunchedEffect(Unit) {

    snapshotFlow {

        query

    }

        .debounce(300)

        .distinctUntilChanged()

        .collectLatest {

            viewModel.search(it)

        }

}
```

Notice how naturally everything fits together.

Compose owns the UI state.

Flow owns the asynchronous pipeline.

`snapshotFlow` simply connects the two.

---

## Another Production Example

Imagine tracking scroll position.

```kotlin
val listState = rememberLazyListState()
```

Your analytics team wants to know whenever the first visible item changes.

Instead of manually observing the state,

you can write:

```kotlin
LaunchedEffect(Unit) {

    snapshotFlow {

        listState.firstVisibleItemIndex

    }

        .collect {

            analytics.logScroll(it)

        }

}
```

Compose observes the UI state.

Flow delivers the updates.

Analytics simply consumes them.

Each layer stays focused on its own responsibility.

---

## The Beautiful Symmetry

Notice what we've learned over the last two chapters.

```text
External World

↓

produceState

↓

Compose State

↓

snapshotFlow

↓

Flow
```

The two APIs solve opposite problems.

One brings information into Compose.

The other sends Compose state into the coroutine world.

Together,

they form a complete bridge.

---

## Why Doesn't Compose State Just Implement Flow?

This is a natural question.

Compose state and Flow were designed for different purposes.

Compose state is optimized for:

- recomposition,
- snapshot consistency,
- UI observation.

Flow is optimized for:

- asynchronous streams,
- operators,
- transformations,
- cancellation,
- pipelines.

Neither replaces the other.

Instead,

Compose gives us bridges between them.

---

## Thinking Like A Production Engineer

Whenever you're writing asynchronous logic,

ask yourself:

> **Does this code naturally belong in a Flow pipeline?**

If the answer is:

> **Yes**

but your data currently exists as Compose state,

`snapshotFlow` is usually the missing bridge.

---

## A Common Misconception

Some developers think:

> **"`snapshotFlow` replaces `StateFlow`."**

It doesn't.

They solve completely different problems.

`StateFlow` usually originates outside Compose.

It often comes from:

- ViewModels,
- repositories,
- use cases,
- business logic.

`snapshotFlow` originates inside Compose.

It converts existing Compose state into a Flow.

One isn't better than the other.

They simply begin in different places.

---

## Bringing Everything Together

Part 4 now forms a complete picture.

```text
External Data

↓

produceState

↓

Compose State

↓

derivedStateOf

↓

Derived Compose State

↓

snapshotFlow

↓

Flow Pipeline
```

Every API answers a different engineering question.

| Question | Solution |
|----------|----------|
| Can this value be calculated from existing state? | `derivedStateOf` |
| How do I bring external data into Compose? | `produceState` |
| How do I expose Compose state as a Flow? | `snapshotFlow` |

Notice something.

This part was never really about three APIs.

It was about understanding how state moves between different parts of an application.

---

## Revision

### Core Idea

`snapshotFlow` converts Compose state into a Flow,

allowing Compose state to participate in coroutine pipelines and Flow operators.

---

### Engineering Mindset

Don't ask:

> **"How can I manually emit Compose state?"**

Ask:

> **"Am I trying to use Compose state inside a Flow pipeline?"**

If yes,

`snapshotFlow` is usually the appropriate bridge.

---

### Key Insight

Compose state and Flow solve different problems.

`snapshotFlow` doesn't replace either one.

It simply connects them.

---

## Completing Part 4 — State Transformation

Congratulations.

You've now learned three different ways state moves through a Compose application.

- Sometimes state is **derived** from other state.
- Sometimes state is **bridged into** Compose.
- Sometimes state is **bridged out of** Compose.

These aren't isolated APIs.

They're three different directions in which state can travel.

Understanding those movements is far more valuable than memorizing the APIs themselves.

---

## One Remaining Question

So far,

we've learned how Compose manages its own state.

A natural question now appears.

> **How does Compose cooperate with the Android lifecycle?**

For example,

what should happen when an Activity moves to the background?

Should Compose continue collecting data?

Or should it pause until the UI becomes visible again?

That begins the next part of the Atlas.

**Lifecycle-Aware State Collection**
