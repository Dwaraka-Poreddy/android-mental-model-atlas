# Keys & Identity

## Observation

In the previous chapter,

we introduced `LaunchedEffect`.

```kotlin
LaunchedEffect(Unit) {

    viewModel.loadData()

}
```

A natural question appears.

> **Why `Unit`?**

Why not:

```kotlin
LaunchedEffect(userId)
```

or

```kotlin
LaunchedEffect(post.id)
```

or even

```kotlin
LaunchedEffect(true)
```

Clearly,

that parameter is important.

But what is Compose actually using it for?

To answer that,

we need to understand one of the most fundamental concepts in the Compose Runtime.

**Identity.**

---

## Forget `LaunchedEffect` For A Moment

This chapter isn't really about `LaunchedEffect`.

It's about a question the Compose Runtime asks constantly.

Whenever something changes,

Compose tries to answer:

```text
Is This

The Same Thing?

or

A Different Thing?
```

That simple question influences a surprising amount of Compose's behavior.

---

## A Simple Example

Suppose we're displaying a user profile.

```kotlin
UserProfile(
    userId = 42
)
```

Compose associates the work with the identity represented by `userId = 42`.

A few moments later,

the user opens another profile.

```kotlin
UserProfile(
    userId = 81
)
```

Now Compose evaluates a simple question.

```text
Did The Identity

Change?
```

If the answer is:

> **No**

existing work can continue.

If the answer is:

> **Yes**

previous work may no longer be valid.

A new identity often requires new work.

But how does Compose know what the identity is?

---

## Identity

Compose can't infer what your data represents.

You have to communicate that.

When you write:

```kotlin
LaunchedEffect(userId) {

    loadProfile(userId)

}
```

you're really saying:

> **This coroutine belongs to the identity represented by `userId`.**

That's a much better mental model than:

> Restart when `userId` changes.

The coroutine isn't restarting simply because a value changed.

It's restarting because the **identity of the work changed**.

The work for User 42 is no longer correct when the current identity becomes User 81.

Compose cancels the old coroutine,

then launches a new one for the new identity.

Exactly the behavior we wanted.

---

## Let's Watch It Happen

Suppose the current user is:

```text
userId = 42
```

Compose launches a coroutine.

```kotlin
loadProfile(42)
```

Now the user selects another profile.

```text
userId = 81
```

Should the coroutine loading User 42 continue?

Of course not.

It belongs to a different user.

Compose cancels the old coroutine,

then starts a new one.

```kotlin
loadProfile(81)
```

Exactly the behavior we wanted.

---

## Why Does `Unit` Work?

Now let's go back.

```kotlin
LaunchedEffect(Unit)
```

What identity does `Unit` represent?

There's only one.

It never changes.

Compose sees:

```text
Same Identity

↓

Keep Existing Coroutine
```

The coroutine continues for the lifetime of that composition.

This is why `Unit` is commonly used for work that should happen once while the composable remains in the composition.

Notice what happened.

We didn't memorize:

> Use `Unit` to run once.

We understood **why** it behaves that way.

---

## Another Example

Imagine a search screen.

```kotlin
LaunchedEffect(searchQuery) {

    search(searchQuery)

}
```

The user types:

```text
A
```

Compose launches a coroutine.

The user continues typing.

```text
An
```

The previous search is no longer relevant.

The identity changed.

Compose cancels the old coroutine,

then launches a new one.

Again,

the behavior follows naturally from identity.

---

## Thinking Like The Runtime

Compose isn't asking:

> **Did the value change?**

It's asking:

```text
Does This

Represent

The Same Identity?
```

Identity determines whether existing work should continue,

or whether new work should begin.

That's a much more powerful mental model.

---

## This Isn't Just About `LaunchedEffect`

Here's the interesting part.

The same idea appears throughout Compose.

Later,

we'll encounter:

```kotlin
remember(userId) {

    ...

}
```

If the identity changes,

Compose discards the old remembered value and creates a new one.

---

We'll also learn:

```kotlin
DisposableEffect(userId) {

    ...

}
```

Again,

the effect belongs to the identity represented by `userId`.

A different identity means different work.

---

Even much later,

when we build lazy lists,

we'll write:

```kotlin
LazyColumn {

    items(
        users,
        key = { it.id }
    ) {

        ...

    }

}
```

Once again,

Compose asks:

```text
Same Item?

or

New Item?
```

Exactly the same concept.

Different API.

---

## Thinking Like A Production Engineer

Whenever an API asks for a key,

don't ask:

> **"What value should I pass?"**

Instead ask:

> **"What identity does this work belong to?"**

That question almost always leads you to the correct key.

---

## A Common Misconception

Many developers think:

> **Keys exist to restart effects.**

That's only one application.

The deeper truth is:

> **Keys communicate identity to the Compose Runtime.**

Restarting,

recreating,

reusing,

or preserving work are all consequences of identity changing.

---

## Bringing Everything Together

The Compose Runtime is constantly making one decision.

```text
                 Identity

                     │

         ┌───────────┴───────────┐

         │                       │

         ▼                       ▼

    Same Identity         Different Identity

         │                       │

         ▼                       ▼

 Continue Existing        Discard Old Work

      Reuse State         Create New Work

    Keep Coroutine       Restart Coroutine

      Keep Effect         New Effect
```

That single decision explains the behavior of many seemingly unrelated Compose APIs.

---

## Revision

### Core Idea

Keys don't tell Compose **what changed**.

They tell Compose **what this work belongs to**.

That identity allows Compose to decide whether existing work can continue or whether new work must begin.

### Engineering Mindset

Don't ask:

> **"What key should I pass?"**

Ask:

> **"What identity does this state, effect, or work represent?"**

### Key Insight

Keys are not unique to `LaunchedEffect`.

They are a fundamental mechanism the Compose Runtime uses to understand identity across multiple APIs.

---

## One Remaining Question

Now that we understand how Compose identifies work,

another question naturally appears.

> **What if my side effect needs both a beginning and an end?**

For example,

registering a listener,

opening a camera,

or subscribing to a callback.

Those operations must be cleaned up when the composable leaves the composition.

That leads us to the next concept.

**DisposableEffect**
