# Mutable State (`MutableState<T>`)

## Observation

In the previous chapter, we learned that Compose represents observable values using:

```kotlin
State<T>
```

A simple question appears.

```text
If State<T> Stores A Value, Why Is There Another Type Called MutableState<T>?
```

---

## The Problem

Sometimes we only want to observe a value.

Sometimes we also need to change it.

Those are two different responsibilities.

Compose represents them using two different types.

---

## A New Mental Model

Think of them like this.

```text
State<T>

↓

Read Only

------------------------

MutableState<T>

↓

Read + Write
```

A `State<T>` lets you observe the current value.

A `MutableState<T>` lets you both observe and modify it.

---

## A Familiar Pattern

If you've worked with Kotlin collections,

this pattern already exists.

```kotlin
List<String>
```

↓

Read only.

```kotlin
MutableList<String>
```

↓

Read + Write.

Compose follows the same idea.

```kotlin
State<Int>
```

↓

Read only.

```kotlin
MutableState<Int>
```

↓

Read + Write.

---

## Connecting It To What We Already Learned

Earlier in the Atlas,

we learned this pattern with `StateFlow`.

```kotlin
private val _uiState = MutableStateFlow(...)

val uiState = _uiState.asStateFlow()
```

The ViewModel owns the mutable version,

while the UI only receives the read-only version.

Compose follows exactly the same design.

```kotlin
private val _count = mutableStateOf(0)

val count: State<Int> = _count
```

Here,

the owner can modify the value,

while everyone else can only observe it.

The design principle is identical.

---

## Why This Design Matters

Imagine every part of your application could modify the same state.

```text
Screen A

↓

Change State

------------------------

Screen B

↓

Change State

------------------------

Random Helper

↓

Change State
```

Understanding who changed the value would quickly become difficult.

Instead,

Compose encourages a simple rule.

```text
One Owner

↓

Many Observers
```

Only the owner modifies the state.

Everyone else observes it.

---

## Production Recognition

When you see:

```kotlin
State<T>
```

think:

```text
Read Only
```

When you see:

```kotlin
MutableState<T>
```

think:

```text
Read + Write
```

---

## Revision

### Core Idea

```text
State<T>

↓

Read Only

------------------------

MutableState<T>

↓

Read + Write
```

### Mental Model

```text
One Owner

↓

MutableState<T>

↓

Expose State<T>

↓

Many Observers
```

### Connection To StateFlow

```text
MutableStateFlow

↓

StateFlow

========================

MutableState<T>

↓

State<T>
```

### Production Recognition

```kotlin
private val _count = mutableStateOf(0)

val count: State<Int> = _count
```

↓

```text
Owner Can Modify Everyone Else Can Observe
```
