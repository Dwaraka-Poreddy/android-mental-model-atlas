# Lazy Lists

## Observation

Imagine we need to display a list containing 10,000 users.

```text
User 1

User 2

User 3

...

User 9,999

User 10,000
```

A natural implementation might be:

```kotlin
Column {

    users.forEach { user ->

        UserRow(user)

    }

}
```

A natural question appears.

> **Does Compose create all 10,000 composables immediately?**

---

## The First Thought

At first,

it might seem reasonable.

After all,

we asked Compose to display 10,000 users.

Shouldn't it build 10,000 UI components?

---

## Let's Think Like The Runtime

Now imagine the phone screen.

Perhaps it can only display:

```text
User 1

User 2

User 3

User 4

User 5

User 6

User 7

User 8
```

Everything else is outside the screen.

Now ask yourself another question.

> **Why spend CPU time creating, measuring, laying out, and drawing User 9,874 when the user can't even see it?**

The work produces no visible pixels.

It provides no immediate value.

---

## A Bigger Engineering Principle

Notice something.

Earlier,

the Compose Runtime asked:

> **Can I skip this recomposition?**

Now it asks a different question.

> **Can I skip creating this UI altogether?**

This is an even bigger optimization.

The cheapest work isn't faster work.

The cheapest work is work that never happens.

---

## A Better Idea

Instead of creating every item immediately,

what if we only created the ones currently visible?

```text
Visible Items

↓

Compose Them

──────────────────

Invisible Items

↓

Don't Create Them Yet
```

As the user scrolls,

Compose can create new items only when they become visible.

That sounds much more efficient.

---

## Introducing `LazyColumn`

This is exactly what `LazyColumn` does.

```kotlin
LazyColumn {

    items(users) { user ->

        UserRow(user)

    }

}
```

The important word isn't **Column**.

It's **Lazy**.

Compose delays work until that work is actually needed.

---

## Understanding The Word "Lazy"

Many developers memorize:

> **"`LazyColumn` is a scrolling version of `Column`."**

That explanation misses the important idea.

The word *lazy* means:

```text
Need This UI?

        │

   ┌────┴────┐

   │         │

  Yes       No

   │         │

   ▼         ▼

Create It   Don't Create It Yet
```

Work happens **on demand**.

Not in advance.

---

## What Happens While Scrolling?

Imagine the screen initially displays:

```text
User 1

User 2

User 3

User 4
```

The user scrolls downward.

```text
User 5

User 6

User 7

User 8
```

Compose now performs new work.

```text
Items Leave Screen

↓

They No Longer Need UI

↓

New Items Become Visible

↓

Compose Those Items
```

Notice something.

Compose isn't trying to keep every item alive forever.

It only keeps alive what the user currently needs.

---

## Doesn't That Lose State?

This is a natural concern.

Suppose Bob's row is expanded.

The user scrolls.

Bob leaves the screen.

Later,

Bob becomes visible again.

How does Compose know this is still Bob?

The answer is exactly what we learned in the previous chapter.

**Keys.**

Keys preserve the logical identity of items,

even as they leave and re-enter the composition.

Without stable keys,

Compose couldn't correctly reconnect remembered state with the correct item.

Notice how the previous chapter naturally supports this one.

---

## `LazyColumn` Is Much More Than Scrolling

It's tempting to think of `LazyColumn` like this.

```text
Column

+

Scrolling
```

That's not really what it is.

Conceptually,

it's closer to:

```text
Column

+

On-Demand Composition

+

On-Demand Measurement

+

On-Demand Layout

+

On-Demand Drawing
```

Compose performs work only when an item actually becomes relevant to the current viewport.

The user only pays for what they can currently see.

---

## Should I Always Use `LazyColumn`?

Not necessarily.

Imagine a simple settings screen.

```text
Profile

Notifications

Privacy

About
```

There are only four items.

Using a `Column` is perfectly reasonable.

A `LazyColumn` introduces additional machinery that provides little benefit for such a small list.

Optimization should solve a real problem,

not an imaginary one.

---

## Thinking Like A Production Engineer

Whenever you're displaying a collection,

ask yourself:

> **Does the user need every item right now?**

If the answer is:

> **No**

then there's little value in creating UI for items that aren't visible.

The runtime should avoid unnecessary work.

---

## A Common Misconception

Many developers think:

> **"`LazyColumn` is faster because it's optimized."**

A more accurate statement is:

> **"`LazyColumn` is faster because it avoids work."**

Compose isn't magically making composition cheaper.

It's simply choosing not to compose UI that isn't currently needed.

That's an important distinction.

---

## Beyond Compose

The same idea appears throughout software engineering.

You'll find similar techniques in:

- RecyclerView
- Virtual scrolling on the web
- Infinite scrolling
- Game engines
- Rendering engines

Different technologies.

Same principle.

Don't create expensive objects until they're actually needed.

---

## Connecting Back To Part 6

Notice the progression we've built.

Earlier,

we learned:

- Immutable models help Compose trust state.
- Stability annotations help Compose trust contracts.
- Keys help Compose trust identity.

Now we've learned another optimization.

Compose avoids creating UI that nobody can currently see.

The runtime keeps asking the same question.

> **Can I avoid unnecessary work while still producing the correct UI?**

Sometimes it skips recomposition.

Sometimes it skips creating UI.

Sometimes it skips measuring.

Sometimes it skips drawing.

Everything points toward the same philosophy.

---

## Bringing Everything Together

```text
      Need To Display A List
              ↓
Does The User Need Every Item Right Now?
              │
      ┌───────┴────────┐
      │                │
     Yes              No
      │                │
      ▼                ▼
Create All      Create Only
Items           Visible Items
                       │
                       ▼
              Compose Work
              Happens Lazily
```

Notice something.

The biggest benefit isn't simply better performance.

It's that the runtime performs only the work required to display the current screen.

Nothing more.

---

## Revision

### Core Idea

`LazyColumn` delays composing list items until they are actually needed.

Instead of eagerly creating the entire list,

it performs work on demand as the user scrolls.

---

### Engineering Mindset

Don't think:

> **"`LazyColumn` is a scrolling `Column`."**

Think:

> **"`LazyColumn` avoids creating UI that the user can't currently see."**

---

### Key Insight

The fastest work isn't optimized work.

The fastest work is work that never happens.

`LazyColumn` achieves better performance primarily by avoiding unnecessary work.

---

## One Remaining Question

We've now learned several ways the Compose Runtime avoids unnecessary work.

A natural question appears.

> **What coding patterns accidentally prevent these optimizations from working?**

That leads us to the final concept in this part.

**Performance Anti-patterns**
