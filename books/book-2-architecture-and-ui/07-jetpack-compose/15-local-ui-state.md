# Local UI State

## Observation

Throughout this document, we've seen two ways to hold state.

Inside a composable:

```kotlin
var query by remember {
    mutableStateOf("")
}
```

Or inside a ViewModel:

```kotlin
val uiState by viewModel.uiState.collectAsState()
```

A simple question appears.

```text
When Should State Live Inside The Composable?

↓

And When Should It Live Inside The ViewModel?
```

---

## A New Mental Model

Instead of memorizing rules,

ask one simple question.

```text
Who Needs This State?
```

The answer usually tells you where the state should live.

---

## When Local UI State Is Enough

If only a single composable needs the state,

the composable itself should own it.

For example:

```text
Password Visibility

↓

Show / Hide
```

```text
Dropdown Menu

↓

Expanded / Collapsed
```

```text
Search Field

↓

Current Text
```

These values only affect the current UI.

No other part of the application needs them.

In these situations,

local UI state is usually the right choice.

---

## Think Of It Like This

Local UI state belongs as close as possible to the UI that needs it.

```text
Composable

↓

Owns Its Own State

↓

Renders Its Own UI
```

This keeps the state simple,

easy to understand,

and easy to maintain.

---

## When Local UI State Is NOT Enough

Imagine the current user.

```text
User Profile
```

or

```text
Shopping Cart
```

or

```text
Search Results
```

Multiple composables need this information.

If every composable kept its own copy,

they could easily become inconsistent.

In these situations,

the state should not be local.

It should have a single owner.

We'll discuss that in the next chapter.

---

## Production Recognition

When you see:

```kotlin
var expanded by remember {
    mutableStateOf(false)
}
```

don't think:

```text
Just Compose State
```

think:

```text
Only This Composable Needs This State
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
Only One Composable Needs It

↓

Local UI State
```

### Production Recognition

```kotlin
remember {
    mutableStateOf(...)
}
```

↓

```text
State Owned By This Composable
```

---

## One Remaining Question

We've learned when state can safely live inside a composable.

But what if multiple composables need the same state?

```text
Who Should Own That State?
```

That leads us to the next concept:

```text
ViewModel UI State
```
