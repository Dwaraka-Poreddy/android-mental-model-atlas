# ViewModel UI State

## Observation

In the previous chapter, we learned that local UI state is a good choice when only a single composable needs a piece of state.

Now consider a slightly different screen.

```kotlin
@Composable
fun UserScreen() {

    UserHeader()

    UserProfile()

}
```

Suppose both composables need to display the current user.

A simple question appears.

```text
Who Should Own The User State?
```

Should each composable keep its own copy?

Or should there be a single owner?

---

## The Problem

Imagine each composable owns its own state.

```text
UserHeader

↓

User = Dwaraka

------------------------

UserProfile

↓

User = Dwaraka
```

Now the user's name changes.

Both composables must update their own copy.

If one updates before the other,

the screen becomes inconsistent.

The same state now exists in multiple places.

---

## A New Mental Model

When multiple composables need the same state,

there should be a single owner.

In Compose,

that owner is usually the ViewModel.

```text
ViewModel

↓

Owns User State

↓

Header Reads It

↓

Profile Reads It

↓

Settings Reads It
```

Instead of every composable maintaining its own copy,

they all observe the same state.

---

## Connecting What We Already Learned

Earlier in the Atlas,

we learned about the **Single Source of Truth**.

This is that principle in action.

```text
Repository

↓

ViewModel

↓

StateFlow<UiState>

↓

Compose
```

The ViewModel owns the state.

Compose simply observes it.

---

## Local UI State vs ViewModel UI State

A simple question helps make the decision.

```text
Who Needs This State?
```

If the answer is:

```text
Only This Composable
```

↓

```text
Local UI State
```

If the answer is:

```text
Multiple Composables
```

↓

```text
ViewModel UI State
```

The owner changes because the scope of the state changes.

---

## Production Recognition

When you see:

```kotlin
val uiState by viewModel.uiState.collectAsState()
```

don't think:

```text
Just Another Compose API
```

think:

```text
The ViewModel Owns Shared State

↓

Compose Observes It
```

---

## Revision

### Core Idea

```text
Ask One Question.

↓

Who Needs This State?
```

### Mental Model

```text
Only One Composable

↓

Local UI State

------------------------

Multiple Composables

↓

ViewModel UI State
```

### Connection To Single Source Of Truth

```text
ViewModel

↓

One Owner

↓

Many Observers
```

### Production Recognition

```kotlin
viewModel.uiState.collectAsState()
```

↓

```text
Shared State Owned By The ViewModel
```

---

# Doc 6 Summary

Throughout this document,

we answered one fundamental question.

```text
How Does Compose Build And Update Modern Android UI?
```

We learned:

```text
Declarative UI

↓

Composable

↓

Composition

↓

Recomposition

↓

State Management

↓

ViewModel Integration
```

You now understand not only how Compose builds and updates the UI,

but also where state should live,

how it flows from the ViewModel to the UI,

and how Compose reacts to changes automatically.

This foundation prepares you for the next document,

where we'll explore the production patterns used in real-world Compose applications.
