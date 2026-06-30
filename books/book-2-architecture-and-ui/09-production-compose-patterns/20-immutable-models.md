# Immutable Models

## Observation

Earlier in the Atlas,

we learned that the Compose Runtime prefers working with stable types.

Stable types allow Compose to safely skip unnecessary recompositions.

A natural question now appears.

> **As developers, how do we actually design models that Compose can trust?**

One of the most effective ways is to design immutable models.

---

## A First Attempt

Imagine we're building a profile screen.

Someone writes:

```kotlin
class User(

    var name: String,

    var age: Int

)
```

At first,

this looks perfectly reasonable.

Now somewhere else in the application,

someone writes:

```kotlin
user.age++
```

A natural question appears.

> **Who knows this happened?**

---

## The Interesting Part

Certainly,

this code knows.

```kotlin
user.age++
```

But what about everyone else?

Does another screen know?

Does another ViewModel know?

Does Compose know?

Not necessarily.

The object silently changed.

Nothing about the object itself tells us that it was modified.

---

## A Bigger Problem

Now imagine the same object is shared.

```text
          User

        /      \

       /        \

Profile Screen   Dashboard

         \

          \

      Settings Screen
```

Suppose the Dashboard executes:

```kotlin
user.age++
```

The Profile screen is now looking at different data.

The Settings screen is also looking at different data.

Yet...

the object reference never changed.

Only something inside it changed.

This makes systems much harder to reason about.

Notice something important.

This isn't just a Compose problem.

It's a software engineering problem.

---

## A Different Way Of Thinking

Instead of changing existing objects,

what if objects never changed after they were created?

Suppose our model looked like this.

```kotlin
data class User(

    val name: String,

    val age: Int

)
```

Now imagine the user's age changes.

Can we write:

```kotlin
user.age++
```

No.

The compiler prevents it.

Instead,

we create a new object.

```kotlin
val updatedUser =

    user.copy(

        age = user.age + 1

    )
```

Notice what happened.

The original object still exists.

The new object represents a new state.

Nothing changed silently.

The transition is explicit.

---

## Why Is This Better?

Imagine reading code six months later.

This:

```kotlin
user.age++
```

raises several questions.

- Who changed it?
- When was it changed?
- Who else was using this object?
- Could another part of the application be affected?

Now compare it with:

```kotlin
val updatedUser =

    user.copy(...)
```

The code communicates something immediately.

> **This represents a new version of the state.**

The previous version still exists.

The transition is obvious.

That's much easier to understand,

debug,

and maintain.

---

## Compose Loves This

Let's connect this back to Compose.

Earlier,

we learned that the Compose Runtime asks:

> **Can I trust this object when deciding whether to skip recomposition?**

Immutable models make that decision much easier.

If an object cannot change after it's is created,

then the only way the UI can receive updated information is to receive a different object.

That's exactly the kind of behavior Compose can reason about confidently.

Notice the wording.

Compose doesn't prefer immutability because immutability is fashionable.

It prefers immutability because immutable objects are predictable.

Predictable objects are much easier to optimize safely.

---

## An Analogy

Imagine a printed book.

Once it's printed,

its contents never change.

If the author wants to update Chapter 5,

they publish a new edition.

Now everyone immediately knows which version they're reading.

Immutable objects work the same way.

A mutable object is more like a shared whiteboard.

Anyone can erase something,

write something new,

or modify existing information.

If the contents changed,

you immediately wonder:

> **Who changed it?**

> **When did they change it?**

> **Did I miss the update?**

Immutable objects eliminate those questions.

Every update creates a new version.

---

## Doesn't Creating New Objects Waste Memory?

This is one of the most common concerns.

> **Creating a new object every time sounds expensive.**

At first,

it does.

But modern runtimes,

including the JVM,

are heavily optimized for allocating short-lived objects.

More importantly,

the benefits usually outweigh the allocation cost.

Immutable models provide:

- predictable behavior,
- easier debugging,
- safer concurrency,
- simpler state management,
- better compatibility with Compose's optimization model.

In practice,

creating a new immutable object is often far cheaper than debugging bugs caused by unexpected mutation.

---

## Thinking Like A Production Engineer

Whenever you're designing a model,

ask yourself:

> **Does this object represent a snapshot of state?**

Or...

> **Will different parts of the application continuously modify this same object?**

For UI,

the first approach is usually much easier to reason about.

Each object becomes a snapshot of the application at a particular moment.

State transitions become explicit.

---

## A Common Misconception

Many developers think:

> **"Immutable models exist for Compose."**

They don't.

Compose simply benefits from them.

You'll find immutable models throughout modern software engineering.

Examples include:

- Redux
- React
- Flutter
- SwiftUI
- Event Sourcing
- Functional Programming

Compose isn't introducing a new idea.

It's embracing one that has proven valuable across many architectures.

---

## Connecting Back To Doc 7

Earlier,

we learned that stable types help Compose safely skip recomposition.

This chapter explains one practical way to help Compose reach that goal.

```text
Immutable Models

↓

Predictable Objects

↓

Easier For Compose To Trust

↓

More Opportunities

To Skip Recomposition
```

Notice something important.

Immutability doesn't guarantee better performance.

It simply creates conditions that allow Compose to optimize safely.

Exactly as we learned in Doc 7.

---

## Bringing Everything Together

```text
Need To Update State

↓

Modify Existing Object?

            │

     ┌──────┴──────┐

     │             │

    Yes           No

     │             │

     ▼             ▼

Hidden        Create A New

Mutation      Immutable Object

     │             │

     ▼             ▼

Harder To     Explicit State

Reason About  Transition

                    │

                    ▼

           Easier For Compose

           To Optimize Safely
```

Notice that the biggest benefit isn't performance.

It's clarity.

Performance is simply a consequence of designing predictable models.

---

## Revision

### Core Idea

Immutable models don't change after they are created.

Instead,

every update produces a new version of the object.

This makes state transitions explicit,

predictable,

and easier for both developers and the Compose Runtime to reason about.

---

### Engineering Mindset

Don't think:

> **"Compose wants immutable models."**

Think:

> **"Immutable models make state changes explicit."**

Compose simply benefits from that property.

---

### Key Insight

Immutability isn't a Compose optimization.

It's a software engineering principle.

Compose's runtime is simply designed to take advantage of predictable, immutable state.

---

## One Remaining Question

Immutable models make it easier for Compose to trust our data.

But sometimes,

the Compose compiler still can't infer that a type behaves correctly.

A natural question appears.

> **Can we explicitly tell Compose that a type follows the stability rules?**

That leads us to the next concept.

**Stability Annotations (`@Immutable`, `@Stable`)**
