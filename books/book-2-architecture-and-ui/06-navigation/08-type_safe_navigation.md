<!--
Primary Question: How can applications safely pass information during navigation?
Prerequisites: Deep Linking
After Reading: You understand why navigation arguments should be type-safe and how validating arguments prevents navigation errors before the application runs.
Next Concept: Nested Navigation Graphs
-->

# Type-Safe Navigation

## Looking Back

In the previous chapter,

we learned that navigation sometimes carries additional information.

For example,

```text
Destination

↓

Product Details + Product ID = 42
```

The destination tells the application where to go.

The arguments tell the destination what it needs to display.

A natural question appears.

```text
How Do We Know Those Arguments Are Correct?
```

---

## The Problem

Imagine navigating to:

```text
Product Details
```

The destination expects:

```text
Product ID
```

Instead,

the application accidentally passes:

```text
User ID
```

Or

```text
Product ID = "abc"
```

instead of

```text
Product ID = 42
```

Or forgets to pass the Product ID entirely.

The application successfully reaches the destination,

but the destination doesn't have the information it needs to function correctly.

---

## A Thought Experiment

Imagine sending a package to a friend.

The destination is correct,

but the shipping information contains mistakes.

For example,

- the house number is missing,
- the postal code is incorrect,
- or the city name is wrong.

The courier reaches the destination,

but cannot complete the delivery.

Navigation works in a very similar way.

Reaching the correct destination isn't enough.

The information carried along the way must also be correct.

---

## Safe Navigation

Safe navigation means ensuring that every destination receives exactly the information it expects.

Conceptually,

```text
Destination + Valid Arguments

↓

Successful Navigation
```

Instead of:

```text
Destination + Unknown Arguments

↓

Unexpected Errors
```

The goal is to prevent invalid navigation requests before they become runtime problems.

---

## Why Type Safety Matters

Imagine discovering a navigation mistake only after users start using the application.

Fixing the issue would require:

- identifying the problem,
- reproducing it,
- releasing an update,
- and waiting for users to install it.

A better approach is to detect mistakes while the application is being developed.

```text
During Development

↓

Find The Mistake

↓

Fix It

↓

Build The Application
```

instead of:

```text
Release The Application

↓

User Finds The Mistake

↓

Crash Or Incorrect Screen
```

Type-safe navigation helps move these mistakes earlier in the development process.

---

## Common Navigation Mistakes

Without validation,

applications commonly encounter problems such as:

```text
Missing Arguments
```

---

```text
Wrong Argument Name
```

---

```text
Wrong Data Type
```

---

```text
Unexpected Null Values
```

These errors often remain hidden until the affected screen is opened.

Type-safe navigation helps prevent these mistakes before the application runs.

---

## Why This Matters

As applications grow, the number of destinations also grows.

Each destination may require different information.

Without a reliable way to validate navigation arguments,

small mistakes become increasingly difficult to find.

Type-safe navigation makes navigation more predictable, more reliable, and easier to maintain.

---

## Common Misconception

A common misconception is:

```text
Type-Safe Navigation = Preventing Navigation
```

Incorrect.

Navigation still happens exactly the same way.

The difference is that incorrect navigation requests are detected much earlier,

before they become runtime failures.

---

## A Natural Question

We've learned why navigation arguments should be validated.

A natural question appears.

```text
How Do Android Applications Actually Guarantee That Navigation Arguments Are Passed Correctly?
```

That leads us to the companion document.

```text
Type-Safe Navigation — Production Code
```

---

## Revision

### Core Idea

```text
Type-Safe Navigation = Ensuring Every Destination Receives The Correct Arguments
```

---

### Mental Model

```text
Destination

↓

Expected Arguments

↓

Validation

↓

Successful Navigation
```

Navigation isn't only about reaching the correct destination.

It's also about arriving with the correct information.

---

### Production Recognition

When thinking about Type-Safe Navigation,

remember that the goal isn't to introduce another navigation mechanism.

The goal is to make existing navigation safer by validating the information passed between destinations.

---

## Looking Ahead

This chapter explained **why** navigation arguments should be validated.

In the companion document,

**Type-Safe Navigation — Production Code**,

we'll explore how Android implements this using:

- Safe Args
- Type-safe routes in Compose Navigation
- Generated navigation classes
- Kotlin Serialization
- Parcelable and Serializable
- SavedStateHandle
- Common production patterns
- Common mistakes and migration strategies

---

### Previous Concept

```text
Deep Linking
```

---

### Next Concept

```text
Type-Safe Navigation — Production Code
```