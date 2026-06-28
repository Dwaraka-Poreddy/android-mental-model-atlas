# How remember Works

## Observation

Earlier, we learned that `remember` preserves a value across recompositions.

For example:

```kotlin
var count by remember {
    mutableStateOf(0)
}
```

Even after the composable recomposes,

the same value is still available.

A simple question appears.

```text
How Does remember Actually Remember The Value?
```

---

## Connecting What We Already Learned

Over the last few chapters,

we learned that the Compose Runtime has:

```text
Composer

↓

Slot Table

↓

Groups

↓

Anchors
```

Those weren't independent concepts.

They all work together to make `remember` possible.

---

## A Thought Experiment

Imagine you're filling out an application form.

The first time,

you submit:

```text
Name

↓

Dwaraka
```

The office stores your application.

A week later,

you return to update another field.

The office doesn't ask you to fill out a brand new form.

Instead,

it retrieves your existing application,

and continues from there.

The information wasn't recreated.

It was remembered.

---

## The First Composition

Now imagine Compose executing this code for the very first time.

```kotlin
var count by remember {
    mutableStateOf(0)
}
```

Compose reaches:

```kotlin
remember { ... }
```

No remembered value exists yet.

So the runtime:

```text
Create The Value

↓

Store It Inside The Slot Table
```

The value is now available for future recompositions.

---

## During Recomposition

Later,

the composable runs again.

Compose reaches the same:

```kotlin
remember { ... }
```

This time,

the runtime does **not** create a new value.

Instead,

it finds the previously stored value.

```text
Find Existing Value

↓

Return It
```

The same object is reused.

That's why the value survives recomposition.

---

## The Complete Picture

Think of the process like this.

```text
First Composition

↓

remember

↓

Runtime Creates Value

↓

Runtime Stores Value

↓

Slot Table

=========================

Next Recomposition

↓

remember

↓

Runtime Finds Value

↓

Reuse Existing Value
```

Nothing is recreated.

The previously remembered value is reused.

---

## The Most Important Idea

One subtle but important detail is easy to miss.

```text
remember Does Not Remember Anything.
```

Instead,

```text
remember Requests A Remembered Value.

↓

The Compose Runtime Stores And Retrieves That Value For You.
```

The runtime does the remembering.

`remember` simply tells the runtime,

"I'll need this value again."

---

## Production Recognition

When you see:

```kotlin
remember {
    ...
}
```

don't think:

```text
Magic
```

think:

```text
Create The Value Once.

↓

Reuse The Existing Value From The Slot Table.
```

---

## Revision

### Core Idea

```text
remember Requests A Remembered Value.

↓

The Runtime Stores And Retrieves It.
```

### Mental Model

```text
First Composition

↓

Create

↓

Store

=========================

Next Recomposition

↓

Find

↓

Reuse
```

### How It Works

```text
Composer

↓

Slot Table

↓

Groups

↓

Anchors

↓

remember
```

### Production Recognition

```kotlin
remember { ... }
```

↓

```text
Create Once. Reuse Forever. (Until The Composition Ends.)
```

---

## One Remaining Question

We've now learned how Compose remembers values.

A natural question appears.

```text
What Happens If The Structure Of The UI Changes?
```

For example,

what if composables are added, removed, or moved?

How does Compose still remember the correct values?

That leads us to the next concept.

```text
Key Changes
```
