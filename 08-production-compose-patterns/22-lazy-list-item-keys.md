# Lazy List Item Keys

## Observation

Imagine we have a simple list.

```text
Alice

Bob

Charlie
```

Compose creates three item composables.

Everything works perfectly.

Now the user inserts a new person at the top.

```text
David

Alice

Bob

Charlie
```

A natural question appears.

> **How does Compose know which item is which?**

---

## The Obvious Answer

At first,

it seems simple.

Compose could compare positions.

```text
Old List

0 → Alice
1 → Bob
2 → Charlie
```

```text
New List

0 → David
1 → Alice
2 → Bob
3 → Charlie
```

Looks reasonable.

Until we examine it more carefully.

---

## The Problem

According to positions,

Compose now believes:

```text
Alice

↓

David
```

```text
Bob

↓

Alice
```

```text
Charlie

↓

Bob
```

Every item appears to have changed.

But that's not what actually happened.

Alice didn't become David.

Bob didn't become Alice.

Charlie didn't become Bob.

We simply inserted one new item.

The identities stayed the same.

Only their positions changed.

---

## Position Isn't Identity

Notice something important.

Compose doesn't actually care about positions.

It cares about identity.

The real question isn't:

> **"Which position is this?"**

It's:

> **"Which logical item is this?"**

Those are two completely different questions.

Identity survives reordering.

Positions don't.

---

## Why Does Identity Matter?

Imagine every row stores some local UI state.

```kotlin
@Composable
fun UserRow(user: User) {

    var expanded by remember {

        mutableStateOf(false)

    }

    ...

}
```

Suppose Bob's row is expanded.

```text
Alice

Bob ▼

Charlie
```

Now David is inserted at the top.

If Compose only uses positions,

the remembered state also moves.

```text
David

Alice ▼

Bob

Charlie
```

Suddenly,

Alice is expanded.

Bob isn't.

Nothing crashed.

Nothing threw an exception.

The UI is simply wrong.

The state followed the position,

not the person.

---

## The Real Problem

Compose doesn't want to preserve positions.

It wants to preserve identity.

Every remembered value,

every animation,

every piece of local state,

should stay attached to the same logical item,

even if that item moves within the list.

---

## Introducing Keys

This is exactly what keys provide.

Instead of identifying an item by its position,

we identify it by something that uniquely represents the item itself.

```kotlin
LazyColumn {

    items(

        items = users,

        key = {

            it.id

        }

    ) { user ->

        UserRow(user)

    }

}
```

Now Compose no longer asks:

> **"Is this item still at index 1?"**

Instead it asks:

> **"Is this still user 42?"**

Even if the position changes,

the identity remains the same.

---

## A Better Mental Model

Without keys,

Compose effectively sees something like this.

```text
Position

↓

Identity
```

With keys,

the relationship changes.

```text
Logical Item

↓

Identity

↓

Current Position
```

The position is now just a property of the item.

It's no longer what defines the item.

---

## This Is Another Trust Contract

Notice something interesting.

Keys are surprisingly similar to the stability annotations we learned in the previous chapter.

`@Stable` tells Compose:

> **"You can trust how this object behaves."**

A key tells Compose:

> **"You can trust this identity."**

Compose builds optimizations on top of that promise.

If the promise is wrong,

the UI becomes wrong.

---

## A Bad Key

Imagine we write:

```kotlin
key = {

    Random.nextInt()

}
```

Every recomposition generates a brand-new identity.

Compose now believes:

Every item is completely new.

All remembered state is discarded.

Animations restart.

Items are recreated.

The key isn't identifying the item.

It's creating a new identity every time.

---

## Another Bad Key

This one is much more common.

```kotlin
key = {

    index

}
```

At first,

this looks reasonable.

Until an item is inserted.

```text
Before

0 → Alice
1 → Bob
2 → Charlie
```

```text
After

0 → David
1 → Alice
2 → Bob
3 → Charlie
```

Every key changes.

Even though Alice is still Alice,

her key changed from `0` to `1`.

We've recreated the original problem.

The key should identify the item,

not where the item currently happens to be.

---

## What Makes A Good Key?

A good key has three important properties.

It should be:

- unique,
- stable over time,
- tied to the logical identity of the item.

Examples include:

- database IDs,
- UUIDs,
- server-generated IDs,
- any value that uniquely identifies that item regardless of where it appears in the list.

---

## An Analogy

Imagine an airport.

Passengers have:

- seat numbers,
- passport numbers.

Seat numbers change.

Passport numbers don't.

Which one identifies the passenger?

The passport.

Positions are like seat numbers.

Keys are like passport numbers.

Compose wants passports.

Not seat assignments.

---

## Thinking Like A Production Engineer

Whenever you're choosing a key,

ask yourself:

> **If I reorder this list tomorrow, will this key still identify the same logical item?**

If the answer is yes,

it's probably a good key.

If the answer is no,

Compose can't preserve identity correctly.

---

## A Common Misconception

Many developers think:

> **"Keys are only a performance optimization."**

Performance is certainly one benefit.

But correctness comes first.

Keys ensure that remembered state,

animations,

focus,

and other UI behavior remain attached to the correct logical item as the list changes.

Performance is simply an additional advantage.

---

## Beyond Compose

Identity is a much bigger concept than Lazy Lists.

You'll encounter the same idea in many technologies.

- React (`key`)
- SwiftUI (`id`)
- RecyclerView (`stableIds`)
- DiffUtil
- Database Primary Keys
- Distributed Systems
- Caching

Different technologies.

Same principle.

State should follow identity,

not position.

---

## Bringing Everything Together

```text
        List Changes
            ↓
How Should Items Be Matched?
            │
     ┌──────┴──────┐
     │             │
     ▼             ▼
Position       Identity
Matching       Matching
     │             │
     ▼             ▼
State Can     State Follows
Move To The   The Correct
Wrong Item    Logical Item
```

Notice something.

The biggest benefit isn't that Compose becomes faster.

It's that Compose continues associating state with the correct item,

even as the list changes.

---

## Revision

### Core Idea

Keys give every logical item a stable identity.

This allows Compose to preserve remembered state,

animations,

and other UI behavior correctly,

even when list positions change.

---

### Engineering Mindset

Don't think:

> **"Keys identify positions."**

Think:

> **"Keys identify logical items."**

Positions change.

Identity doesn't.

---

### Key Insight

State should follow identity,

not position.

Keys are how we communicate that identity to the Compose Runtime.

---

## One Remaining Question

We've now learned how Compose preserves the identity of items inside a list.

A natural question appears.

> **How does Compose avoid creating and composing hundreds or thousands of list items that aren't even visible yet?**

That leads us to the next concept.

**Lazy Lists**
