# Composition Tree

## Observation

In the previous chapter, we learned that the Composer understands our composable functions.

For example:

```kotlin
@Composable
fun ProfileScreen() {

    Column {

        ProfileHeader()

        ProfileBody()

    }

}
```

A simple question appears.

```text
The Composer Now Understands My Composable Functions.

↓

How Does It Remember Their Structure?
```

---

## A Thought Experiment

Imagine drawing the relationships between the composables.

```text
      ProfileScreen

           ↓

          Column

       ↙         ↘

ProfileHeader   ProfileBody
```

Nothing magical happened.

The nested function calls naturally formed a hierarchy.

Every parent composable contains its child composables.

---

## A New Mental Model

The Composer represents this hierarchy internally as a tree.

This tree is called the **Composition Tree**.

Think of it like this.

```text
      ProfileScreen

           ↓

          Column

       ↙         ↘

ProfileHeader   ProfileBody
```

Every composable becomes a node in the tree.

Whenever one composable calls another,

a parent-child relationship is created.

The runtime now understands how all the composables are connected.

---

## Why The Composition Tree Exists

Imagine Compose had no tree.

Whenever something changed,

it would have to repeatedly inspect your Kotlin code to understand the UI structure.

Instead,

the Composer already has a structured representation.

```text
Composable Functions

↓

Composer

↓

Composition Tree
```

From this point onward,

the runtime can work with the tree instead of repeatedly analyzing your source code.

---

## What The Composition Tree Represents

The Composition Tree does **not** represent pixels on the screen.

Instead,

it represents the relationships between composables.

Think of it as answering questions like:

```text
Who Is The Parent?

↓

Who Are The Children?

↓

Which Composable Belongs Where?
```

It is the runtime's understanding of your composable hierarchy.

---

## Production Recognition

When you see:

```kotlin
Column {

    Header()

    Content()

    Footer()

}
```

don't think:

```text
Just Nested Function Calls
```

think:

```text
A Tree Of Composable Relationships
```

---

## Revision

### Core Idea

```text
Nested Composable Functions

↓

Composition Tree
```

### Mental Model

```text
Composable Functions

↓

Composer

↓

Composition Tree
```

### What It Represents

```text
Parent

↓

Children

↓

Composable Hierarchy
```

### Production Recognition

```kotlin
Column {

    Header()

    Content()

}
```

↓

```text
Hierarchy Of Composable Relationships
```

---

## One Remaining Question

The Composition Tree helps the runtime understand the relationships between composables.

A natural question appears.

```text
If The Composition Tree Represents Composable Relationships,

↓

What Represents The Actual UI?
```

That leads us to the next concept.

```text
UI Tree
```
