# Groups

## Observation

In the previous chapter, we learned that the Slot Table is the Compose Runtime's memory.

```text
Slot Table

↓

Remembered Information
```

A simple question appears.

```text
If The Slot Table Stores So Much Information,

↓

How Does Compose Keep It Organized?
```

---

## A Thought Experiment

Imagine a library.

Thousands of books are stored inside.

Now imagine every book is simply thrown onto the floor.

```text
Programming Book

History Book

Science Book

Math Book

Programming Book

History Book
```

Finding anything would quickly become difficult.

Instead,

the library organizes books into shelves.

```text
Shelf A

↓

Programming Books

------------------------

Shelf B

↓

History Books

------------------------

Shelf C

↓

Science Books
```

Each shelf groups related books together.

Finding information becomes much easier.

---

## Connecting It To Compose

Compose faces a very similar problem.

The Slot Table stores information for many different composables.

Instead of placing everything into one giant collection,

Compose organizes related information into **Groups**.

Think of it like this.

```text
ProfileScreen

↓

Group

------------------------

Header

↓

Group

------------------------

Content

↓

Group

------------------------

Footer

↓

Group
```

You can think of each composable as having its own group.

Everything related to that composable is stored together.

---

## A New Mental Model

Groups are the organizational units inside the Slot Table.

```text
Slot Table

↓

Groups

↓

Remembered Information
```

Instead of searching through one huge collection,

Compose can quickly locate the information associated with a particular composable.

---

## Why Groups Matter

Imagine only:

```kotlin
Header()
```

needs to be updated.

Compose doesn't have to search the entire Slot Table.

It can immediately locate the group associated with:

```text
Header()
```

Groups make the runtime organized,

and that organization becomes one of the reasons Compose can update the UI efficiently.

---

## What Groups Do NOT Do

Groups do **not** represent pixels,

or UI components,

or screens.

They simply organize the runtime's memory.

We'll see in the upcoming chapters how Compose uses these groups to remember values and efficiently perform recomposition.

---

## Production Recognition

When you hear:

```text
Group
```

don't think:

```text
UI Component
```

think:

```text
An Organizational Unit Inside The Slot Table
```

---

## Revision

### Core Idea

```text
Groups

↓

Organize The Slot Table
```

### Mental Model

```text
Slot Table

↓

Groups

↓

Remembered Information
```

### Responsibilities

```text
Slot Table

↓

Stores Information

------------------------

Groups

↓

Organize That Information
```

### Production Recognition

```text
Group
```

↓

```text
Compose's Way Of Organizing Runtime Memory
```

---

## One Remaining Question

Groups organize the Slot Table.

A natural question appears.

```text
How Does Compose Find The Same Group Again During A Future Recomposition?
```

That leads us to the next concept.

```text
Anchors
```
