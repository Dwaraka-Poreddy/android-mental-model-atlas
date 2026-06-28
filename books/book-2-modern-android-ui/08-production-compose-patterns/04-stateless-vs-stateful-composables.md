# Stateless vs Stateful Composables

## Observation

In the previous chapter,

we learned that every piece of state has an owner.

A natural question appears.

> **Once we've identified the owner, should the composable store the state itself or receive it from somewhere else?**

Let's explore that through a real production example.

---

## A Production Example

Suppose you're building a reusable **Like** button.

Your first implementation might look like this.

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

It works perfectly.

Now imagine the requirements evolve.

- The initial liked state comes from the server.
- Another device can update the liked status.
- A parent composable needs to know when the user likes a post.
- The total number of liked posts is displayed elsewhere on the screen.

Pause for a moment.

**Should this composable really own the `liked` state?**

---

## Connecting It To The Previous Chapter

Our decision framework was simple.

```text
New State

↓

Who Owns It?

↓

Choose The Owner

↓

Choose The State Container
```

Let's apply it.

Who owns whether a post is liked?

Not the button.

The button simply displays the current state and reports user interactions.

The actual owner is the application's business state.

Once we realize that,

the implementation changes naturally.

---

## A Stateless Design

Instead of storing the state,

the composable receives it.

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

Notice what's missing.

- No `remember`
- No `mutableStateOf`

The composable doesn't own the information.

It simply displays it.

---

## Two Types Of Composables

This naturally leads to two categories.

### Stateful Composable

A stateful composable owns state.

```kotlin
@Composable
fun ExpandableCard() {

    var expanded by remember {
        mutableStateOf(false)
    }

    ...
}
```

It is responsible for storing,

updating,

and reacting to that state.

---

### Stateless Composable

A stateless composable receives state.

```kotlin
@Composable
fun LikeButton(
    liked: Boolean,
    onLikeChanged: (Boolean) -> Unit
)
```

It doesn't own the information.

It simply renders the UI and reports user interactions.

---

## Does Stateless Mean Better?

A common misunderstanding is:

> **"Stateless composables are always better."**

Not necessarily.

That would be another overcorrection.

Some state genuinely belongs inside the composable.

For example,

a password field might manage whether the password is currently visible.

```kotlin
var passwordVisible by remember {
    mutableStateOf(false)
}
```

Or a dropdown menu might remember whether it is expanded.

```kotlin
var expanded by remember {
    mutableStateOf(false)
}
```

These states don't represent business information.

They only control local UI behavior.

Keeping them inside the composable is often the best design.

---

## A Better Decision Process

Instead of asking:

> **"Should this composable be stateless?"**

ask:

> **"Does this composable actually own this information?"**

If the answer is:

> **Yes**

a stateful composable is perfectly reasonable.

If the answer is:

> **No**

the composable should probably receive the state instead.

Ownership determines the answer.

Not preference.

---

## Why Stateless Composables Are So Popular

Stateless composables are often easier to:

- reuse
- preview
- test
- combine
- reason about

Because they behave much like pure functions.

Given the same inputs,

they always produce the same UI.

But remember.

This is a benefit,

not the goal.

The goal is still correct state ownership.

---

## A Common Production Mistake

Imagine someone writes:

```kotlin
@Composable
fun UserCard() {

    var user by remember {
        mutableStateOf<User?>(null)
    }

    ...
}
```

Instead of immediately suggesting a different API,

first ask the architectural question.

> **Why does this composable own the user?**

Often,

that single question leads to a much cleaner design than discussing APIs.

---

## Thinking Like A Production Engineer

Experienced Compose developers don't scan code looking for `remember`.

They scan code looking for **ownership**.

Whenever they find local state,

they ask:

> **"Should this composable really be responsible for this information?"**

That question catches architectural problems long before they become implementation problems.

---

## What This Chapter Does NOT Say

This chapter does **not** say:

- Stateless is good.
- Stateful is bad.
- Every composable should receive all its state.

Those are oversimplifications.

The real engineering principle is much more useful.

> **A composable should own only the state it is responsible for.**

Everything else naturally follows from that decision.

---

## Revision

### Core Idea

```text
State Ownership

↓

Stateful

or

Stateless
```

### Engineering Mindset

Don't ask:

> **"Should this composable be stateless?"**

Ask:

> **"Should this composable own this information?"**

### Key Insight

Stateless and stateful are not architectural goals.

They are consequences of choosing the correct state owner.

---

## One Remaining Question

We've decided that some state shouldn't live inside the composable.

A natural question appears.

> **How do we move that state to the correct owner while still allowing the composable to read and update it?**

That leads us to the next concept.

**State Hoisting**
