# Event-Driven Coroutines (`rememberCoroutineScope`)

## Observation

So far,

every side effect we've learned starts automatically.

- `LaunchedEffect` starts when the composable enters the composition.
- `DisposableEffect` performs setup when the composable enters the composition.
- `SideEffect` runs after every successful composition.

Notice something.

In every case,

**Compose decides when the work begins.**

A natural question appears.

> **What if I don't want Compose to decide?**

> **What if I want the user to decide?**

---

## A Production Example

Suppose the user taps a button.

After the tap,

we want to show a snackbar.

The API is a suspend function.

```kotlin
snackbarHostState.showSnackbar("Saved!")
```

Our first instinct might be:

```kotlin
Button(
    onClick = {

        snackbarHostState.showSnackbar(
            "Saved!"
        )

    }
) {

    Text("Save")

}
```

Immediately,

the compiler complains.

Why?

Because `showSnackbar()` is a suspend function.

Like we learned earlier in the Atlas,

suspend functions must execute inside a coroutine.

---

## The First Thought

After learning `LaunchedEffect`,

many developers naturally think:

> **"I'll just launch a coroutine using `LaunchedEffect`."**

```kotlin
LaunchedEffect(Unit) {

    snackbarHostState.showSnackbar(
        "Saved!"
    )

}
```

Technically,

this launches a coroutine.

But when does it execute?

Immediately when the composable enters the composition.

Not when the user presses the button.

We've chosen the wrong lifecycle.

---

## The Lifecycle We Actually Want

This is the behavior we really need.

```text
Screen Appears

↓

Do Nothing

↓

User Clicks Button

↓

Launch Coroutine

↓

Coroutine Completes

↓

Wait For Next Click
```

Notice something.

Compose shouldn't decide when this coroutine starts.

The user should.

---

## Introducing `rememberCoroutineScope`

Earlier in the Atlas,

we learned that every coroutine needs a `CoroutineScope`.

Compose provides one that's owned by the current composable.

```kotlin
@Composable
fun SaveButton() {

    val scope = rememberCoroutineScope()

    Button(

        onClick = {

            scope.launch {

                snackbarHostState.showSnackbar(
                    "Saved!"
                )

            }

        }

    ) {

        Text("Save")

    }

}
```

Notice what happened.

The coroutine doesn't start when the screen appears.

It starts only when the user clicks the button.

Exactly the lifecycle we wanted.

---

## Why Does Compose Provide Its Own CoroutineScope?

A natural question appears.

> **Why not create my own `CoroutineScope`?**

Because Compose already knows the lifetime of this composable.

If the user leaves the screen while the coroutine is still running,

Compose automatically cancels the scope returned by `rememberCoroutineScope()`.

Just like `LaunchedEffect`,

the coroutine participates in the composable lifecycle.

The difference is **who decides when it starts.**

---

## A Production Example

Imagine saving a draft.

```kotlin
@Composable
fun EditorScreen() {

    val scope = rememberCoroutineScope()

    Button(

        onClick = {

            scope.launch {

                saveDraft()

            }

        }

    ) {

        Text("Save")

    }

}
```

Now imagine the user navigates away before the save operation completes.

Because the coroutine belongs to the composable's scope,

Compose automatically cancels it when the composable leaves the composition.

The coroutine lifecycle remains consistent with the UI lifecycle.

---

## Comparing It With `LaunchedEffect`

Both APIs launch coroutines.

The important difference is **who starts them.**

| Engineering Problem | API |
|----------------------|-----|
| Coroutine should start automatically when the composable enters the composition | `LaunchedEffect` |
| Coroutine should start because of a user action or another event | `rememberCoroutineScope` |

That's the real distinction.

Not automatic versus manual.

But **who owns the start of the coroutine.**

---

## Thinking Like A Production Engineer

Whenever you need a coroutine,

ask yourself one question.

> **Who should decide when this coroutine starts?**

If the answer is:

> **The composable entering the composition**

use `LaunchedEffect`.

If the answer is:

> **A user action or another event**

use `rememberCoroutineScope`.

That single question usually leads to the correct API.

---

## A Common Misconception

Many developers summarize this API as:

> **"Use it inside `onClick`."**

That's true,

but it isn't the reason the API exists.

The real purpose is much broader.

It allows **event-driven coroutines** to participate in the lifecycle of the current composable.

The button click is just one example.

The same idea applies to:

- gesture callbacks,
- menu selections,
- swipe actions,
- drag events,
- and any other event that should trigger asynchronous work.

---

## Bringing Everything Together

The Side Effects section now forms a complete decision framework.

```text
External Work

↓

Who Should Start It?

            │

     ┌──────┴──────┐

     │             │

     ▼             ▼

Compose        User/Event

     │             │

     ▼             ▼

LaunchedEffect   rememberCoroutineScope
```

Instead of memorizing APIs,

we're asking a much better engineering question.

> **Who owns the start of this work?**

---

## Revision

### Core Idea

`rememberCoroutineScope()` provides a `CoroutineScope` owned by the current composable,

allowing user actions and other events to launch coroutines while still respecting the composable lifecycle.

### Engineering Mindset

Don't ask:

> **"Which API launches a coroutine?"**

Ask:

> **"Who should decide when this coroutine starts?"**

If Compose should decide,

use `LaunchedEffect`.

If an event should decide,

use `rememberCoroutineScope`.

### Key Insight

`LaunchedEffect` and `rememberCoroutineScope` both launch coroutines.

The difference isn't the coroutine.

The difference is **who owns the lifecycle that starts it.**

---

## One Remaining Question

So far,

every side effect we've learned follows a clear lifecycle.

- `LaunchedEffect` starts a coroutine.
- `DisposableEffect` manages setup and cleanup.
- `SideEffect` publishes committed Compose state.
- `rememberCoroutineScope` lets events launch coroutines.

A natural question still remains.

> **Suppose you've started a long-running coroutine—for example, collecting a `Flow` or running a timer—and that coroutine should continue running without interruption.**

Now imagine the composable recomposes because one of the values used inside that coroutine has changed.

> **How can the coroutine observe the latest value from recomposition without being cancelled and started all over again?**

Restarting the coroutine would give it the latest value,

but it would also interrupt work that should continue.

Keeping the coroutine alive avoids that interruption,

but then how does it see the new value?

Compose provides a solution for exactly this problem.

**Long-Lived Effects With Latest State (`rememberUpdatedState`)**
