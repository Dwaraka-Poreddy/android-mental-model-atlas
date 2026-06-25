# Anchors

## Observation

In the previous chapter, we learned that the Slot Table organizes its information into Groups.

```text
Slot Table

↓

Groups
```

A simple question appears.

```text
During The Next Recomposition,

↓

How Does Compose Find The Same Group Again?
```

Searching the entire Slot Table every time would quickly become inefficient.

There has to be a better way.

---

## A Thought Experiment

Imagine a huge library containing thousands of books.

Suppose you're looking for one particular book.

You could walk through every shelf until you eventually find it.

```text
Shelf 1

↓

Shelf 2

↓

Shelf 3

↓

...
```

That would be slow.

Instead,

the library maintains a catalog.

You search the catalog,

and it immediately tells you where the book is.

```text
Book

↓

Library Catalog

↓

Correct Shelf
```

The catalog doesn't store the book.

It simply helps you find it.

Compose uses a similar idea.

An **Anchor** helps Compose quickly find the correct group inside the Slot Table.

---

## Connecting It To Compose

Compose faces a very similar problem.

Groups already exist inside the Slot Table.

During future recompositions,

Compose needs a reliable way to find those same groups again.

That reference is called an **Anchor**.

Think of it like this.

```text
Group

↓

Anchor

↓

Find The Same Group Again
```

The Anchor doesn't store the group's information.

It simply helps Compose locate the correct group when it needs it.

---

## A New Mental Model

Groups store information.

Anchors help Compose find that information again.

```text
Groups

↓

Store Information

------------------------

Anchors

↓

Locate Information
```

The two work together.

---

## Why Anchors Matter

Imagine only:

```kotlin
Header()
```

needs to recompose.

Compose doesn't want to search through every group in the Slot Table.

Instead,

it uses an Anchor to quickly locate the correct group and continue its work.

Without Anchors,

finding previously stored information would become much more expensive.

---

## What Anchors Do NOT Do

Anchors do **not**:

```text
Store Remembered Values

↓

Represent UI Components

↓

Render The UI
```

Their responsibility is much simpler.

```text
Help Compose Find The Right Group Again
```

---

## Production Recognition

When you hear:

```text
Anchor
```

don't think:

```text
Another Runtime Object
```

think:

```text
A Way For Compose To Find The Same Group Again
```

---

## Revision

### Core Idea

```text
Anchors

↓

Help Compose Find Groups
```

### Mental Model

```text
Groups

↓

Store Information

------------------------

Anchors

↓

Find That Information
```

### Responsibilities

```text
Slot Table

↓

Stores Information

↓

Groups

↓

Organize Information

↓

Anchors

↓

Locate Information
```

### Production Recognition

```text
Anchor
```

↓

```text
Find The Same Group Again
```

---

## One Remaining Question

We now know that Compose can:

- store information
- organize it into groups
- find those groups again

A natural question appears.

```text
How Does remember Actually Use All Of This?
```

That leads us to the next concept.

```text
How remember Works
```
