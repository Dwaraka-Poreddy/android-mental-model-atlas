# Observable State (`State<T>`)

## Observation

In the previous chapter, we learned that `mutableStateOf()` creates a value that Compose can observe.

For example:

```kotlin
var count by remember {
    mutableStateOf(0)
}
```

A simple question appears.

```text
Why Doesn't mutableStateOf() Simply Return 0?
```

Why wrap the value inside another object?

---

## The Problem

A normal value can only store data.

For example:

```kotlin
val age = 25
```

The variable simply contains:

```text
25
```

Nothing more.

If the value changes,

the variable itself has no way to notify Compose.

It is just a value.

---

## A New Mental Model

Compose needs something more than just the value.

It also needs to know:

```text
When The Value Changes
```

To achieve this,

Compose wraps the value inside a **State object**.

Instead of returning:

```text
0
```

it returns something like:

```text
State

↓

Value = 0
```

This object is able to store the value **and** communicate with Compose when the value changes.

This is why the type is called:

```kotlin
State<T>
```

Think of it as:

```text
An Object That Holds A Value Compose Can Observe
```

---

## Normal Value vs Observable State

A normal value looks like this.

```text
age

↓

25
```

An observable state looks like this.

```text
count

↓

State Object

↓

Value = 0
```

The important difference is not the value.

The difference is the object surrounding it.

That object gives Compose a way to observe changes.

---

## Why Compose Uses State Objects

A plain value can answer:

```text
What Is The Current Value?
```

A `State<T>` object can answer:

```text
What Is The Current Value?

↓

And Notify Compose When It Changes
```

That extra capability is what makes automatic recomposition possible.

---

## Production Recognition

When you see:

```kotlin
State<Int>
```

don't think:

```text
Just An Int
```

think:

```text
An Object Containing An Int That Compose Can Observe
```

---

## Revision

### Core Idea

```text
Observable State

(State<T>) = An Object That Holds A Value Compose Can Observe
```

### Mental Model

```text
Normal Value

↓

Stores Data

------------------------

State<T>

↓

Stores Data

+

Notifies Compose When The Value Changes
```

### Production Recognition

```kotlin
State<T>
```

↓

```text
Observable Value Wrapped Inside A State Object
```
