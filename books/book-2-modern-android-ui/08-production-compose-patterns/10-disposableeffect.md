# DisposableEffect

## Observation

In the previous chapter,

we learned that `LaunchedEffect` manages the lifecycle of a coroutine.

When the composable leaves the composition,

Compose automatically cancels the coroutine.

That works perfectly for coroutines.

But not every side effect is a coroutine.

Consider these examples.

```kotlin
registerNetworkListener()
```

```kotlin
sensorManager.registerListener(...)
```

```kotlin
camera.open()
```

Each of these operations raises the same question.

> **Who cleans this up?**

Unlike a coroutine,

these resources won't automatically release themselves.

Someone has to do it.

---

## The Lifecycle We Actually Want

Before introducing any API,

let's describe the behavior we want.

```text
Composable Enters
Composition

↓

Acquire Resource

↓

Recomposition

↓

Do Nothing

↓

Composable Leaves
Composition

↓

Release Resource
```

Notice something.

This lifecycle has two distinct phases.

- Setup
- Cleanup

Whenever a composable acquires a resource,

it should also define how that resource is released.

---

## Introducing `DisposableEffect`

This is exactly what `DisposableEffect` provides.

```kotlin
DisposableEffect(Unit) {

    registerNetworkListener()

    onDispose {

        unregisterNetworkListener()

    }

}
```

Notice the symmetry.

```text
Enter Composition

↓

Setup

↓

Leave Composition

↓

Cleanup
```

Everything acquired during setup is released during cleanup.

---

## A Production Example

Suppose we need to observe network connectivity.

```kotlin
DisposableEffect(Unit) {

    val listener = ...

    connectivityManager
        .register(listener)

    onDispose {

        connectivityManager
            .unregister(listener)

    }

}
```

Let's walk through what happens.

### First Composition

The composable enters the composition.

The listener is registered.

---

### Recomposition

The UI recomposes.

Nothing happens.

The existing listener continues to receive updates.

---

### Leaving Composition

The composable leaves the composition.

`onDispose` executes.

The listener is unregistered.

The resource has been cleaned up correctly.

---

## Another Production Example

Suppose we want to observe the Android lifecycle.

```kotlin
DisposableEffect(lifecycleOwner) {

    val observer = ...

    lifecycleOwner.lifecycle
        .addObserver(observer)

    onDispose {

        lifecycleOwner.lifecycle
            .removeObserver(observer)

    }

}
```

Once again,

notice the pattern.

Whatever we set up,

we also tear down.

The previous chapter already taught us what the key means.

If the `lifecycleOwner` identity changes,

Compose disposes the old effect and creates a new one.

No additional explanation is needed.

---

## The Engineering Principle

`DisposableEffect` isn't about listeners.

It's about **resources with a lifetime**.

Whenever you acquire something,

ask yourself:

> **Who is responsible for releasing it?**

If the answer is:

> **"When this composable leaves the composition."**

then `DisposableEffect` is usually the correct solution.

---

## Thinking Like A Production Engineer

Whenever you write code like this:

```kotlin
register(...)

subscribe(...)

open(...)

addObserver(...)
```

pause for a moment.

Ask yourself:

> **Where does this get cleaned up?**

If you can't immediately point to the cleanup,

there's a good chance you've discovered a lifecycle bug,

or even a memory leak.

---

## Comparing It With `LaunchedEffect`

Both APIs manage lifecycles,

but they manage different kinds of work.

| `LaunchedEffect` | `DisposableEffect` |
|------------------|--------------------|
| Manages a coroutine | Manages a resource |
| Starts asynchronous work | Performs setup |
| Coroutine is cancelled automatically | Cleanup is defined explicitly |
| Best for suspend functions and `Flow` collection | Best for listeners, observers and registrations |

Notice the pattern.

Neither API exists because Compose likes adding APIs.

Each one exists because a different kind of work has a different lifecycle.

---

## A Common Misconception

Many developers think:

> **`DisposableEffect` is only for listeners.**

That's just the most common example.

The real rule is much broader.

Use it whenever you acquire something that must later be released.

Examples include:

- Broadcast receivers
- Lifecycle observers
- Sensor listeners
- Camera resources
- Media players
- Callback registrations
- SDK subscriptions

The API is about lifecycle management,

not about a specific Android component.

---

## Bringing Everything Together

The previous chapters now fit together naturally.

```text
Why Side Effects Exist

↓

Work Should Follow
The Correct Lifecycle

────────────────────────

LaunchedEffect

↓

Coroutine Lifecycle

────────────────────────

Keys & Identity

↓

What Work
Belongs Together?

────────────────────────

DisposableEffect

↓

Setup

↓

Cleanup
```

Compose isn't providing unrelated APIs.

It's providing different tools for different lifecycle problems.

---

## Revision

### Core Idea

Whenever a composable acquires a resource,

it should also define how that resource is released.

`DisposableEffect` allows both operations to follow the lifecycle of the composable.

### Engineering Mindset

Don't ask:

> **"Can I register this listener here?"**

Ask:

> **"Who will unregister it?"**

If the answer is:

> **"When this composable leaves the composition."**

then `DisposableEffect` is usually the right tool.

### Key Insight

`DisposableEffect` isn't a cleanup API.

It's a lifecycle management API for resources that require both setup and explicit teardown.

---

## One Remaining Question

We've learned how to manage long-lived work such as coroutines,

listeners,

and observers.

A natural question appears.

> **What if I simply need to notify the outside world that a successful composition has just completed?**

For example,

updating a non-Compose object with the latest UI state.

That leads us to the next concept.

**SideEffect**
