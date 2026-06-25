# Composer

## Observation

In the previous chapter, we learned that composable functions describe the UI.

```text
Composable Function

↓

UI Description
```

We also learned that the Compose Runtime is responsible for understanding those descriptions.

A simple question appears.

```text
Who Inside The Compose Runtime Actually Reads My Composable Functions?
```

---

## A Thought Experiment

Imagine an architect designing a house.

The architect creates a blueprint.

```text
Blueprint
```

The blueprint itself does not become a house.

Before construction can begin,

someone must first study the blueprint,

understand it,

and determine what needs to be built.

```text
Blueprint

↓

Architect Reads The Blueprint

↓

Builder

↓

House
```

The blueprint describes.

The architect understands.

The builder constructs.

Each has a different responsibility.

---

## Connecting It To Compose

Compose works in a similar way.

```text
Composable Function

↓

Compose Runtime

↓

Actual UI
```

But the Compose Runtime is made up of many different components.

The first component responsible for understanding your composable functions is called the **Composer**.

Think of it as:

```text
Composable Function

↓

Composer

↓

Compose Runtime Continues Building The UI
```

---

## What The Composer Does

The Composer reads the UI description written inside your composable functions.

For example,

```kotlin
@Composable
fun Greeting() {

    Text("Hello")

    Button(
        onClick = { }
    ) {
        Text("Continue")
    }

}
```

The Composer understands that this screen contains:

```text
Greeting

↓

Text

↓

Button

↓

Text
```

It doesn't directly draw the UI.

Instead,

it understands the structure of your composable functions and prepares the information the rest of the runtime needs.

---

## What The Composer Does NOT Do

The Composer does **not**:

```text
Draw Pixels

↓

Measure Views

↓

Render The Screen
```

Those responsibilities belong to other parts of the Compose Runtime that we'll explore later.

For now,

think of the Composer as the component that understands your composable functions.

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

The Composer Understands Those Instructions.
```

---

## Revision

### Core Idea

```text
Composer

↓

Understands Composable Functions
```

### Mental Model

```text
Composable Function

↓

Composer

↓

Runtime Continues Building The UI
```

### Production Recognition

```text
Composer
```

↓

```text
The Part Of Compose That Understands Your UI Description
```

---

## One Remaining Question

The Composer now understands the structure of our composable functions.

A natural question appears.

```text
How Does The Composer Represent That Structure Internally?
```

That leads us to the next concept.

```text
Composition Tree
```
