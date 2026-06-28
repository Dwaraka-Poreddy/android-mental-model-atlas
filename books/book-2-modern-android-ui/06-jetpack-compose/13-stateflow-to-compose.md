# StateFlow → Compose

## Observation

In the previous documents, we learned that the ViewModel owns the screen's state.

For example:

```kotlin
class CounterViewModel : ViewModel() {

    val uiState = ...

}
```

We also learned that Compose is responsible for rendering the UI.

```kotlin
@Composable
fun CounterScreen() {

}
```

A simple question appears.

```text
The State Lives Inside The ViewModel.

↓

How Does Compose Get That State?
```

---

## Connecting What We Already Know

Earlier in the Atlas, we learned this architecture.

```text
Repository

↓

ViewModel

↓

UI State
```

Compose simply becomes the next consumer.

```text
Repository

↓

ViewModel

↓

StateFlow<UiState>

↓

Compose
```

The ViewModel still owns the state.

Compose simply reads it.

Nothing about the ownership changes.

---

## A New Mental Model

Think of Compose as a subscriber.

```text
ViewModel

↓

Produces UI State

------------------------

Compose

↓

Consumes UI State
```

The ViewModel decides **what** the current UI state is.

Compose decides **how** to render that state.

Each has a different responsibility.

---

## Why This Matters

Notice what **doesn't** happen.

Compose does **not** create its own copy of the screen state.

```text
Compose

✗ Owns State
```

Instead,

it observes the state already owned by the ViewModel.

```text
ViewModel

↓

Owns UI State

↓

Compose Reads UI State
```

This keeps the ViewModel as the Single Source of Truth.

---

## Production Recognition

When you see a composable receiving a ViewModel,

don't think:

```text
Compose Stores The Screen State
```

think:

```text
ViewModel Owns The State

↓

Compose Reads The State
```

---

## Revision

### Core Idea

```text
ViewModel Owns UI State

↓

Compose Reads UI State
```

### Mental Model

```text
Repository

↓

ViewModel

↓

StateFlow<UiState>

↓

Compose
```

### Production Recognition

```text
Compose Does Not Own Screen State
```

↓

```text
Compose Reads State Owned By The ViewModel
```

---

---

## One Remaining Question

The ViewModel owns the UI state.

```text
ViewModel

↓

StateFlow<UiState>
```

Compose renders that state.

```text
Compose

↓

UI
```

But...

`StateFlow` is just a stream of values.

```text
How Does Compose Listen To That Stream?
```

That leads us to the next concept.

```kotlin
collectAsState()
```
