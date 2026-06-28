# mutableStateOf

## Observation

In the previous chapter, we learned that `remember` keeps a value alive across recompositions.

For example:

```kotlin
@Composable
fun Counter() {

    var count = remember {
        0
    }

    Text("$count")

}
```

Now suppose the value changes.

```kotlin
count++
```

A simple question appears.

```text
The Value Changed.

↓

How Does Compose Know?
```

Should the UI update automatically?

Or does Compose have no idea that anything changed?

---

## The Problem

`remember` only remembers a value.

It does **not** tell Compose when that value changes.

Imagine this.

```text
count

0

↓

1
```

To us,

the value clearly changed.

But Compose is not constantly checking every variable inside every composable.

So another question naturally follows.

```text
How Can Compose Notice That A Value Has Changed?
```

---

## A New Mental Model

Compose solves this problem using:

```kotlin
mutableStateOf(...)
```

Instead of storing a normal value,

it stores a value that Compose can observe.

Think of it as:

```text
Value Changes

↓

Compose Is Notified

↓

Recomposition Happens
```

Unlike a normal variable,

changes to this value are automatically visible to Compose.

---

## Example

```kotlin
@Composable
fun Counter() {

    var count by remember {
        mutableStateOf(0)
    }

    Text("$count")

}
```

When the value changes,

```kotlin
count++
```

Compose is immediately notified.

It knows that the UI depending on `count` may need to be updated.

---

> **📝 Side Note — What Does `by` Mean?**
>
> You might have noticed this syntax:
>
> ```kotlin
> var count by remember {
>     mutableStateOf(0)
> }
> ```
>
> The `by` keyword is **not** a Compose feature.
>
> It is a Kotlin language feature called **Property Delegation**.
>
> Without `by`, the same code would look like this:
>
> ```kotlin
> val count = remember {
>     mutableStateOf(0)
> }
>
> Text("${count.value}")
>
> count.value++
> ```
>
> `mutableStateOf()` returns a `MutableState<Int>` object, and the actual value is stored inside its `value` property.
>
> Using `by` is simply Kotlin's shorthand for accessing that `value` property automatically.
>
> So these are equivalent:
>
> ```kotlin
> count++
> ```
>
> and
>
> ```kotlin
> count.value++
> ```
>
> Throughout this Atlas, we'll usually use the `by` syntax because it's the style you'll see in most modern Compose code. We'll learn how property delegation works in a later chapter.

---

## What mutableStateOf Does

Without `mutableStateOf`:

```text
Value Changes

↓

Compose Knows Nothing
```

With `mutableStateOf`:

```text
Value Changes

↓

Compose Is Notified

↓

Recomposition
```

That is the purpose of `mutableStateOf`.

---

## What mutableStateOf Does NOT Do

`mutableStateOf` does **not**:

```text
Keep A Value Alive Across Recompositions
```

That is the job of:

```kotlin
remember
```

`mutableStateOf` has a different responsibility.

It allows Compose to observe changes to a value.

Together, they solve two different problems.

```text
remember

↓

Keeps The Value Alive

------------------------

mutableStateOf

↓

Notifies Compose When The Value Changes
```

---

## Production Recognition

When you see:

```kotlin
var count by remember {
    mutableStateOf(0)
}
```

don't think:

```text
Just

A Mutable Variable
```

think:

```text
A Mutable Value That Compose Can Observe
```

---

## Revision

### Core Idea

```text
mutableStateOf = A Value That Compose Can Observe
```

### Mental Model

```text
Value Changes

↓

Compose Is Notified

↓

Recomposition
```

### Relationship With remember

```text
remember

↓

Keeps The Value Alive

------------------------

mutableStateOf

↓

Notifies Compose When The Value Changes
```

### Production Recognition

```kotlin
mutableStateOf(...)
```

↓

```text
Changing This Value Automatically Notifies Compose
```
