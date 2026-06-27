# Why Side Effects Exist

## Observation

Throughout the Atlas,

we've repeatedly described composable functions in the same way.

> **Composable functions describe the UI.**

Their responsibility is to transform state into UI.

Nothing more.

A natural question appears.

> **If composables are just describing the UI, why does Compose provide APIs like `LaunchedEffect`, `DisposableEffect`, and `SideEffect`?**

To answer that,

we first need to understand something much deeper.

---

## The Real Problem Isn't "Doing Work"

Imagine this screen.

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

If you've been following the Atlas,

your first reaction is probably:

> "Wait...

> `loadData()` will execute every recomposition."

Exactly.

Notice something.

We're **not** calling a repository directly from the UI.

The ViewModel still owns the business logic.

Yet the code is still wrong.

Why?

---

## Let's Think Like The Runtime

From Doc 7,

we already know that composables may execute:

- when entering the composition,
- after state changes,
- after parent recomposition,
- after configuration changes,
- whenever Compose determines they need to run again.

Now think about this line.

```kotlin
viewModel.loadData()
```

If the composable executes again,

what happens?

Another call.

Another recomposition?

Another call.

Very quickly,

we've tied data loading to the recomposition lifecycle.

That isn't the lifecycle we wanted.

We wanted:

> **Load data once when the screen appears.**

Those are completely different things.

---

## Another Production Example

Navigation.

```kotlin
@Composable
fun LoginScreen(...) {

    if (loginSucceeded) {
        navController.navigate("home")
    }

}
```

Looks harmless.

Until recomposition happens.

Now Compose evaluates the same code again.

```text
navigate()

navigate()

navigate()
```

The navigation itself wasn't wrong.

The lifecycle that triggered it was.

---

## Another Example

Showing a snackbar.

```kotlin
if (showError) {

    snackbarHostState.showSnackbar(
        "Something went wrong."
    )

}
```

Imagine the composable recomposes three times before `showError` changes.

How many snackbars should appear?

One.

How many actually appear?

Potentially three.

Again,

the problem isn't the snackbar.

The problem is that recomposition became the trigger.

---

## Another Example

Analytics.

```kotlin
analytics.logScreen("Home")
```

Suppose the screen recomposes five times.

Your analytics now report:

```text
Home

Home

Home

Home

Home
```

The user only visited the screen once.

The analytics event should have been tied to the screen lifecycle,

not the recomposition lifecycle.

---

## The Big Realization

Composable functions are expected to be **safe to execute multiple times**.

That means executing the same composable repeatedly should never accidentally:

- reload data,
- navigate,
- show another snackbar,
- register another listener,
- log another analytics event,
- start another coroutine.

If recomposition changes the behavior of your application,

then the work probably belongs to a different lifecycle.

---

## There Isn't Just One Lifecycle

This is the key insight.

A Compose application has multiple lifecycles.

```text
Recomposition Lifecycle
        │
        └── Describe UI

────────────────────────────

Screen Lifecycle
        │
        └── Initial data loading

────────────────────────────

Event Lifecycle
        │
        ├── Navigation
        ├── Snackbar
        └── User actions

────────────────────────────

Coroutine Lifecycle
        │
        └── Background work

────────────────────────────

Listener Lifecycle
        │
        ├── Register
        └── Unregister
```

Notice something.

Only one of these is the recomposition lifecycle.

Most real-world operations belong somewhere else.

---

## Why Side Effects Exist

Compose intentionally separates these lifecycles.

Composable functions remain responsible for describing the UI.

Side Effect APIs are responsible for coordinating work that belongs to other lifecycles.

Think of it like this.

```text
Composable

↓

Describe UI

↓

Compose Decides
When To Recompose

────────────────────────

Side Effect APIs

↓

Perform External Work

↓

According To The
Correct Lifecycle
```

This separation makes recomposition safe,

predictable,

and repeatable.

---

## Thinking Like A Production Engineer

Whenever you write code inside a composable,

pause for a moment and ask yourself:

> **If this composable executes 20 times, should this operation also execute 20 times?**

If the answer is:

> **Yes**

it's probably just normal UI logic.

If the answer is:

> **No**

you've probably identified a Side Effect.

The next question becomes:

> **Which lifecycle should actually control this operation?**

That question naturally leads you to the correct Side Effect API.

---

## A Common Misconception

Some developers hear:

> "Composable functions shouldn't perform side effects."

and conclude:

> "Composable functions shouldn't contain any logic."

That's not true.

Simple calculations are perfectly safe.

```kotlin
val greeting =
    if (user.isPremium)
        "Welcome back!"
    else
        "Hello!"
```

```kotlin
val fullName =
    "${user.firstName} ${user.lastName}"
```

These computations are deterministic.

Running them multiple times doesn't change the behavior of the application.

The problem isn't computation.

The problem is **operations whose execution should be governed by a lifecycle other than recomposition.**

---

## Bringing Everything Together

Instead of memorizing Side Effect APIs,

remember this principle.

```text
Does This Operation
Belong To The
Recomposition Lifecycle?

        │
   ┌────┴────┐
   │         │
 Yes         No
   │         │
   ▼         ▼
Normal UI   Side Effect
 Logic         API
```

Every Side Effect API in Compose exists to solve one problem:

> **How do we execute work according to the correct lifecycle instead of the recomposition lifecycle?**

---

## Revision

### Core Idea

Composable functions should be safe to execute repeatedly.

If repeating a composable changes the behavior of your application,

the work probably belongs to a different lifecycle.

### Engineering Mindset

Don't ask:

> **"Can I call this function from a composable?"**

Ask:

> **"Which lifecycle should control this operation?"**

### Key Insight

Side Effect APIs don't exist because Compose wants more APIs.

They exist because **real applications have multiple lifecycles**, and recomposition is only one of them.

---

## One Remaining Question

We've learned that some work should happen according to the **screen lifecycle** instead of the **recomposition lifecycle**.

A natural question appears.

> **How do we launch a coroutine exactly when a composable enters the composition and automatically cancel it when the composable leaves?**

That leads us to the next concept.

**LaunchedEffect**
