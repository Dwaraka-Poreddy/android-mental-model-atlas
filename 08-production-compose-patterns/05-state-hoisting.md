# State Hoisting

## Observation

In the previous chapter,

we learned that a composable should only own the state it is responsible for.

A natural question appears.

> **If a composable shouldn't own a piece of state, how does it still read and update it?**

Let's continue with the same example.

---

## Revisiting Our Like Button

We changed our composable from this.

```kotlin
@Composable
fun LikeButton() {

    var liked by remember {
        mutableStateOf(false)
    }

    Button(
        onClick = {
            liked = !liked
        }
    ) {
        Text(
            if (liked) "Liked"
            else "Like"
        )
    }
}
```

to this.

```kotlin
@Composable
fun LikeButton(
    liked: Boolean,
    onLikeChanged: (Boolean) -> Unit
) {
    Button(
        onClick = {
            onLikeChanged(!liked)
        }
    ) {
        Text(
            if (liked) "Liked"
            else "Like"
        )
    }
}
```

The button no longer owns the state.

A natural question appears.

> **Where does `liked` come from now?**

---

## The Parent Owns The State

Since we already decided that the button isn't the owner,

the responsibility moves to its parent.

```kotlin
@Composable
fun FeedScreen() {

    var liked by remember {
        mutableStateOf(false)
    }

    LikeButton(
        liked = liked,
        onLikeChanged = {
            liked = it
        }
    )
}
```

Notice what changed.

The state moved **out of the child**

and **into the parent**.

This is exactly what **State Hoisting** means.

---

## What Does "Hoisting" Mean?

The word sounds more complicated than the idea itself.

Initially,

the ownership looked like this.

```text
LikeButton

↓

Owns State
```

After moving the state,

the relationship becomes:

```text
FeedScreen

↓

Owns State

↓

LikeButton

↓

Displays State
```

The ownership moved upward in the composable hierarchy.

That's why it's called **State Hoisting**.

---

## Why Is This Better?

At first glance,

this seems like more code.

So why is it considered a better design?

Because the parent can now:

- initialize the state,
- restore the state,
- share the state,
- observe state changes,
- test the state,
- replace the implementation later.

Meanwhile,

the child becomes much simpler.

Its only responsibility is rendering the UI and reporting user interactions.

---

## A Production Example

Imagine a screen with two composables.

```kotlin
LikeButton(...)

LikeCounter(...)
```

Both need access to the same information.

If the button owns the state,

the counter can't see it.

You now have two options.

### Option 1

Duplicate the state.

```text
LikeButton

↓

liked = true


LikeCounter

↓

liked = true
```

Now you have two sources of truth.

Keeping them synchronized quickly becomes difficult.

---

### Option 2

Move the state to their common parent.

```text
FeedScreen

↓

liked = true

├── LikeButton
└── LikeCounter
```

Now both composables receive the same state from the same owner.

There's only one source of truth.

This isn't just a Compose recommendation.

It's a natural consequence of correct ownership.

---

## State Flows Down

Once the parent owns the state,

it passes the current value to its children.

```text
FeedScreen

↓

liked

↓

LikeButton
```

The child never needs to know:

- where the state came from,
- how it is stored,
- or how it is updated.

It simply receives the current value.

---

## Events Flow Up

What happens when the user clicks the button?

The child doesn't modify the state directly.

Instead,

it reports the event.

```text
FeedScreen

↑

onLikeChanged()

↑

LikeButton
```

The parent receives the event,

updates the state,

and sends the new value back down.

This creates a simple and predictable data flow.

---

## Connecting It To UDF

This should feel familiar.

Earlier in the Atlas,

we learned **Unidirectional Data Flow**.

```text
State

↓

UI

↓

User Interaction

↓

Event

↓

State Update

↓

New State
```

State Hoisting isn't introducing a new architectural pattern.

It's simply how Compose applies Unidirectional Data Flow at the composable level.

---

## Thinking Like A Production Engineer

Whenever you see:

```kotlin
remember {
    mutableStateOf(...)
}
```

don't immediately ask:

> **"Should I hoist this?"**

Instead ask:

> **"Who owns this information?"**

If the owner is somewhere else,

hoisting is usually the natural consequence.

Notice the order.

Ownership comes first.

Hoisting comes second.

---

## A Common Overcorrection

After learning State Hoisting,

some developers start moving **every** piece of state upward.

Eventually,

parent composables end up with dozens of parameters,

while child composables become difficult to read.

That's not better architecture.

If a piece of state genuinely belongs to a composable,

leave it there.

State Hoisting is a tool.

It isn't the goal.

The goal is correct ownership.

---

## Bringing Everything Together

The previous three chapters are really answering one question.

```text
Who Owns This Information?

↓

Composable

↓

Stateful

====================

Parent / ViewModel

↓

State Hoisting

↓

Stateless
```

Once ownership is clear,

everything else naturally follows.

---

## Revision

### Core Idea

```text
Correct Owner

↓

Owns State

↓

Passes State Down

↓

Receives Events Up
```

### Engineering Mindset

Don't ask:

> **"Should I hoist this state?"**

Ask:

> **"Who should own this information?"**

State Hoisting is simply the implementation of that decision.

### Key Insight

State Hoisting isn't about moving state higher.

It's about moving state to the **correct owner**.

---

## One Remaining Question

We've learned how to move state to the correct owner.

A natural question appears.

> **What does State Hoisting look like in larger applications with dozens of composables and multiple layers of UI?**

That leads us to the next concept.

**State Hoisting Patterns**
