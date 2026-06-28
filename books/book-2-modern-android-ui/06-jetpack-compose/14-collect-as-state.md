# collectAsState()

## Observation

In the previous chapter, we learned that the ViewModel owns the screen's state.

```text
ViewModel

↓

StateFlow<UiState>

↓

Compose
```

Now suppose the ViewModel emits new UI states.

```text
Count = 0

↓

Count = 1

↓

Count = 2

↓

...
```

A simple question appears.

```text
How Does Compose Continuously Receive These Values?
```

---

## The Problem

A `StateFlow` continuously emits new values.

Simply accessing it isn't enough.

```kotlin
viewModel.uiState
```

This only gives us the `StateFlow` object.

It does **not** tell Compose to observe future updates.

Compose needs a way to:

```text
Receive The Latest Value

↓

Update The UI Whenever A New Value Arrives
```

---

## A New Mental Model

Compose provides:

```kotlin
collectAsState()
```

Think of it as:

```text
Listen To A StateFlow

↓

Always Keep The Latest Value Available To Compose
```

Whenever the `StateFlow` emits a new value,

Compose automatically receives it and updates the affected UI.

---

## Example

```kotlin
@Composable
fun CounterScreen(
    viewModel: CounterViewModel
) {

    val uiState by viewModel.uiState.collectAsState()

    Text("${uiState.count}")

}
```

Whenever the ViewModel emits a new `CounterUiState`,

`collectAsState()` receives the latest value,

and Compose automatically recomposes the UI where needed.

---

## Think Of It As A Bridge

The ViewModel and Compose work with different kinds of state.

```text
ViewModel

↓

StateFlow

↓

collectAsState()

↓

Compose State

↓

UI
```

`collectAsState()` is the bridge that connects these two worlds.

---

## Connecting What We Already Learned

Earlier in this document, we learned how local state works.

```kotlin
var count by remember {
    mutableStateOf(0)
}
```

Here,

the state belongs to the composable itself.

Now compare it with:

```kotlin
val uiState by viewModel.uiState.collectAsState()
```

Here,

the state belongs to the ViewModel.

Compose simply observes it.

```text
remember

↓

Composable Owns State

------------------------

collectAsState()

↓

ViewModel Owns State

↓

Compose Observes It
```

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
Listen To The ViewModel's StateFlow And Always Keep The UI In Sync
```

---

## Revision

### Core Idea

```text
collectAsState() = Observe A StateFlow Inside Compose
```

### Mental Model

```text
ViewModel

↓

StateFlow

↓

collectAsState()

↓

Compose

↓

UI
```

### Relationship With remember

```text
remember

↓

Local State

------------------------

collectAsState()

↓

ViewModel State
```

### Production Recognition

```kotlin
viewModel.uiState.collectAsState()
```

↓

```text
Continuously Observe The ViewModel's State Inside Compose
```
