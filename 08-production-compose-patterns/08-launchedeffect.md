# LaunchedEffect

## Observation

In the previous chapter,

we learned that some work shouldn't follow the **recomposition lifecycle**.

Instead,

it should follow a different lifecycle.

A natural question appears.

> **Suppose I need to launch a coroutine when a screen appears. How do I tie that coroutine to the lifecycle of the composable instead of the lifecycle of recomposition?**

That's exactly the problem `LaunchedEffect` solves.

---

## The Wrong Approach

Suppose we have a `HomeViewModel`.

```kotlin
class HomeViewModel : ViewModel() {

    fun loadData() {
        ...
    }

}
```

Now imagine this screen.

```kotlin
@Composable
fun HomeScreen(
    viewModel: HomeViewModel
) {

    val uiState by
        viewModel.uiState.collectAsState()

    viewModel.loadData()

    HomeContent(uiState)

}
```

We already know why this is problematic.

Every recomposition executes:

```kotlin
viewModel.loadData()
```

again.

The data loading isn't wrong.

The lifecycle controlling it is.

---

## The Lifecycle We Actually Want

Let's describe the desired behavior before introducing any API.

We don't want this.

```text
Composable Recomposes

↓

Launch Coroutine
```

Instead,

we want this.

```text
Composable Enters
Composition

↓

Launch Coroutine

↓

Recomposition

↓

Do Nothing

↓

Composable Leaves
Composition

↓

Cancel Coroutine
```

Notice something important.

The coroutine has its own lifecycle.

It starts when the composable enters the composition.

It ends when the composable leaves the composition.

Recomposition shouldn't affect it.

---

## Introducing `LaunchedEffect`

This is exactly what `LaunchedEffect` provides.

```kotlin
@Composable
fun HomeScreen(
    viewModel: HomeViewModel
) {

    val uiState by
        viewModel.uiState.collectAsState()

    LaunchedEffect(Unit) {
        viewModel.loadData()
    }

    HomeContent(uiState)

}
```

Let's walk through what happens.

### First Composition

The composable enters the composition.

The coroutine launches.

`loadData()` executes.

Exactly what we wanted.

---

### Recomposition

Suppose the UI recomposes because new state arrives.

The coroutine is **not** launched again.

The existing coroutine simply continues.

The UI updates.

Nothing more.

---

### Leaving Composition

Suppose the user navigates away.

The composable leaves the composition.

Compose automatically cancels the coroutine.

No cleanup code.

No manual cancellation.

No leaked work.

---

## Why Does `LaunchedEffect` Launch A Coroutine?

Many operations that belong to the composable lifecycle are asynchronous.

For example:

- Loading initial data
- Calling suspend functions
- Collecting a `Flow`
- Running animations
- Waiting with `delay()`

All of these naturally require coroutines.

Instead of creating your own `CoroutineScope`,

Compose provides one whose lifetime is already tied to the composable.

---

## A Production Example

Suppose your ViewModel exposes UI events.

```kotlin
val events: Flow<UiEvent>
```

The screen needs to observe them.

```kotlin
LaunchedEffect(Unit) {

    viewModel.events.collect { event ->

        // Handle event

    }

}
```

When the screen appears,

collection starts.

When the screen disappears,

collection automatically stops.

No additional lifecycle management is required.

---

## Another Production Example

Imagine a splash screen.

After two seconds,

navigate to the home screen.

```kotlin
LaunchedEffect(Unit) {

    delay(2000)

    navController.navigate("home")

}
```

Now imagine the user leaves the screen after one second.

What should happen?

Nothing.

Because the coroutine is automatically cancelled,

navigation never occurs.

The behavior matches the lifecycle of the screen.

---

## Thinking Like A Production Engineer

Whenever you need a coroutine,

don't immediately ask:

> **"Where can I launch this?"**

Instead ask:

> **"What should control the lifetime of this coroutine?"**

If the answer is:

> **"This composable."**

then `LaunchedEffect` is usually the right choice.

Notice how different that question is.

We're not choosing an API.

We're choosing the correct lifecycle.

---

## A Common Misconception

Many developers summarize `LaunchedEffect` like this.

> **"It runs once."**

That's not actually what it guarantees.

`LaunchedEffect` guarantees that the coroutine follows the lifecycle of the composable.

Whether it launches once,

restarts,

or launches again later depends on something we haven't discussed yet.

Its **key**.

We'll explore that in the next chapter.

For now,

assume the coroutine is tied to the lifetime of the current composition.

---

## Bringing Everything Together

The previous chapter taught us this principle.

> **External work should follow the correct lifecycle.**

This chapter answers a specific question.

> **How do we tie a coroutine to the lifecycle of a composable?**

The answer is:

> **`LaunchedEffect`**

Not because it's a special Compose API,

but because it gives your coroutine exactly the lifecycle it needs.

---

## Revision

### Core Idea

```text
Composable Enters

↓

Launch Coroutine

↓

Recomposition

↓

Do Nothing

↓

Composable Leaves

↓

Cancel Coroutine
```

### Engineering Mindset

Don't ask:

> **"Where should I launch this coroutine?"**

Ask:

> **"Which lifecycle should control this coroutine?"**

### Key Insight

`LaunchedEffect` doesn't exist to launch coroutines.

It exists to tie a coroutine to the **lifecycle of a composable**, making it independent of recomposition.

---

## One Remaining Question

We've intentionally ignored one important detail.

```kotlin
LaunchedEffect(Unit)
```

Why `Unit`?

What happens if we write:

```kotlin
LaunchedEffect(userId)
```

or

```kotlin
LaunchedEffect(post.id)
```

What do these values actually mean?

That leads us to the next concept.

**Keys & Identity**
