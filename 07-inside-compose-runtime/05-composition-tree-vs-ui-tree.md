# Composition Tree vs UI Tree

## Observation

In the previous chapters, we learned that the Compose Runtime maintains two different trees.

```text
Composition Tree

↓

UI Tree
```

A natural question appears.

```text
Why

Does Compose Need Two Trees?

↓

Wouldn't One Tree Be Enough?
```

---

## A Thought Experiment

Imagine an architect designing a house.

The architect first creates a blueprint.

```text
Blueprint
```

Later,

the construction team builds the actual house.

```text
Blueprint

↓

House
```

A simple question appears.

```text
Should We Throw Away The Blueprint Once Construction Begins?
```

Of course not.

The blueprint and the house serve different purposes.

The blueprint helps us understand the design.

The house is what actually exists.

Both are needed.

---

## Connecting It To Compose

Compose works in a very similar way.

The runtime keeps two different representations of your UI.

```text
Composition Tree

↓

Understand The UI
```

```text
UI Tree

↓

Prepare The UI For Rendering
```

One helps the runtime understand what the developer described.

The other helps the runtime prepare what will eventually be displayed on the screen.

---

## Why Compose Needs Both

Imagine this composable.

```kotlin
@Composable
fun ProfileScreen() {

    Header()

    Content()

    Footer()

}
```

Now suppose only:

```kotlin
Header()
```

changes.

First,

Compose looks at the Composition Tree.

```text
Which Composable Changed?
```

Once it knows the answer,

it updates the corresponding part of the UI Tree.

```text
Update The UI Representation
```

Each tree has a different responsibility.

Neither can completely replace the other.

---

## Think Of It Like This

```text
Composition Tree

↓

Understand The UI

------------------------

UI Tree

↓

Prepare The UI For Rendering
```

One answers:

```text
What Did The Developer Describe?
```

The other answers:

```text
What Will Compose Render?
```

---

## Production Recognition

When you hear:

```text
Composition Tree
```

think:

```text
Understanding The UI
```

When you hear:

```text
UI Tree
```

think:

```text
Preparing The UI For Rendering
```

---

## Revision

### Core Idea

```text
Composition Tree

↓

Understand The UI

------------------------

UI Tree

↓

Prepare The UI For Rendering
```

### Mental Model

```text
Composable Functions

↓

Composition Tree

↓

UI Tree

↓

Screen
```

### Responsibilities

```text
Composition Tree

↓

Composable Hierarchy

------------------------

UI Tree

↓

UI Representation
```

### Production Recognition

```text
Composition Tree ≠ UI Tree
```

↓

```text
Different Representations For Different Responsibilities
```

---

## One Remaining Question

We now understand that the Compose Runtime maintains several internal structures to understand and prepare the UI.

A natural question appears.

```text
How Does Compose Remember Information Between Recompositions?
```

That leads us to the next concept.

```text
Slot Table
```
