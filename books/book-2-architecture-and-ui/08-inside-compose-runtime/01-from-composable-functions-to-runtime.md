# From Composable Functions To Runtime

## Observation

Throughout the previous document, we've been writing composable functions.

For example:

```kotlin
@Composable
fun Greeting() {
    Text("Hello")
}
```

When this code runs,

an actual UI appears on the screen.

A simple question appears.

```text
This Is Just A Kotlin Function.

↓

How Did It Become An Actual UI?
```

---

## A Thought Experiment

Imagine an architect designing a house.

The architect creates a blueprint.

```text
Blueprint
```

The blueprint is **not** the house.

It is simply a description of what the house should look like.

Someone else reads that blueprint and builds the actual house.

```text
Blueprint

↓

Builder

↓

House
```

The blueprint describes.

The builder constructs.

---

## Connecting It To Compose

A composable function works in a very similar way.

```kotlin
@Composable
fun Greeting() {
    Text("Hello")
}
```

This function does **not** directly create buttons,

text,

or layouts.

Instead,

it describes what the UI should look like.

Think of it as:

```text
Composable Function

↓

UI Description
```

---

## The Missing Piece

A question naturally appears.

```text
If The Composable Only Describes The UI,

↓

Who Actually Builds The UI?
```

Something has to read those descriptions.

Something has to understand:

```kotlin
Text("Hello")

Button(...)

Column(...)
```

and turn them into something Android can display.

---

## A New Mental Model

That responsibility belongs to the **Compose Runtime**.

Think of it as an engine.

```text
Composable Function

↓

Compose Runtime

↓

Actual UI
```

The runtime reads the UI description written inside your composable functions,

understands it,

and builds everything needed to display the screen.

---

## Looking Ahead

The Compose Runtime doesn't build the UI directly.

Instead,

it creates several internal structures that help it understand,

remember,

and efficiently update your UI.

We'll explore each of those structures throughout this document.

For now,

the important idea is simply this:

```text
Composable Functions Describe The UI.

↓

The Compose Runtime Understands Those Descriptions.
```

---

## Production Recognition

When you see:

```kotlin
@Composable
fun UserProfile() {
    ...
}
```

don't think:

```text
This Function Creates The UI
```

think:

```text
This Function Describes The UI.

↓

The Compose Runtime Builds The Actual UI.
```

---

## Revision

### Core Idea

```text
Composable Functions Describe The UI.

↓

Compose Runtime Builds The Actual UI.
```

### Mental Model

```text
Composable Function

↓

UI Description

↓

Compose Runtime

↓

Actual UI
```

### Production Recognition

```kotlin
@Composable
fun Screen() { ... }
```

↓

```text
Description

↓

Runtime

↓

UI
```

---

## One Remaining Question

We now know that the Compose Runtime is responsible for understanding our composable functions.

A natural question appears.

```text
What Exactly Is The Compose Runtime?
```

That leads us to the next concept.

```text
Composer
```
