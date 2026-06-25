# rememberSaveable

## Observation

In the previous chapters, we learned that `remember` keeps a value alive across recompositions.

For example:

```kotlin
var count by remember {
    mutableStateOf(0)
}
```

Suppose the user taps the button several times.

```text
Count

0

↓

5
```

Everything works as expected.

Now the user rotates the device.

Suddenly,

the screen shows:

```text
Count 0
```

instead of:

```text
Count 5
```

A simple question appears.

```text
I Thought remember Remembered The Value.

↓

Why Was It Lost?
```

---

## The Problem

`remember` only remembers values while the current composition exists.

Think of it like this.

```text
Composition Created

↓

remember Works

------------------------

Composition Destroyed

↓

remember Is Lost
```

When the screen is recreated,

Compose creates a brand new composition.

Since the previous composition no longer exists,

everything remembered inside it is lost.

---

## A New Mental Model

Sometimes,

we want a value to survive more than just recomposition.

We want it to survive screen recreation as well.

Compose provides:

```kotlin
rememberSaveable
```

Think of it as:

```text
remember

↓

Survives Recomposition

------------------------

rememberSaveable

↓

Survives Recomposition + Screen Recreation
```

---

## Example

```kotlin
var count by rememberSaveable {
    mutableStateOf(0)
}
```

Now suppose the user rotates the device.

```text
Count 5

↓

Rotate Device

↓

Count 5
```

The value is automatically restored.

---

## What rememberSaveable Does

`rememberSaveable` solves a different problem than `remember`.

```text
remember

↓

Keep The Value Alive Across Recompositions

------------------------

rememberSaveable

↓

Keep The Value Alive Across Recompositions + Screen Recreation
```

One builds upon the other.

---

## What rememberSaveable Does NOT Do

`rememberSaveable` does **not**:

```text
Persist Data Forever
```

If the app is completely removed from memory,

or the user clears app data,

the value is still lost.

Its purpose is simply to survive temporary screen recreation.

We'll learn how this works internally in a later chapter.

---

## Production Recognition

When you see:

```kotlin
rememberSaveable { ... }
```

don't think:

```text
Permanent Storage
```

think:

```text
Restore This Value When The Screen Is Recreated
```

---

## Revision

### Core Idea

```text
rememberSaveable = Remember A Value Across Recompositions And Screen Recreation
```

### Mental Model

```text
remember

↓

Recomposition

------------------------

rememberSaveable

↓

Recomposition + Screen Recreation
```

### Production Recognition

```kotlin
rememberSaveable { ... }
```

↓

```text
Restore This Value After The Screen Is Recreated
```
---

> **📝 Side Note — Does `rememberSaveable` Restore The Value Every Time I Reopen A Screen?**
>
> Not necessarily.
>
> `rememberSaveable` is primarily designed for situations where **Android recreates the current screen**, such as after a device rotation or other temporary system-driven recreation.
>
> For example:
>
> ```text
> Counter Screen
>
> ↓
>
> Rotate Device
>
> ↓
>
> Counter Screen Recreated
> ```
>
> In this case, `rememberSaveable` restores the saved value.
>
> However, if you navigate away from a screen and later open it again, a completely new screen may be created. In that situation, `rememberSaveable` does **not** automatically restore the previous value.
>
> Similarly, `rememberSaveable` is **not** a replacement for a database, local storage, or ViewModel state.
>
> Think of it as:
>
> ```text
> remember
>
> ↓
>
> Remembers While The Current Composition Exists
>
> ------------------------
>
> rememberSaveable
>
> ↓
>
> Also Restores Saveable Values When Android Recreates The Current Screen
> ```
>
> As we learned in the **Lifecycle** chapter, Android may recreate a screen in situations such as device rotation. `rememberSaveable` integrates with that lifecycle to restore supported values when the screen is recreated.
