# Navigation & Arguments — Production Code

## Purpose

Understand how production Android applications navigate between destinations, pass data safely, and organize navigation arguments in large codebases.

---

## Looking Back

In the previous chapter, we learned how destinations communicate by navigating to one another and passing arguments.

This chapter focuses on how production Android applications implement those navigation flows while keeping them safe, maintainable, and easy to understand.

---

# Recognition

When exploring a production Android project, you'll commonly encounter code such as:

```kotlin
navController.navigate(...)
```

```kotlin
composable(
    route = ...
)
```

```kotlin
SavedStateHandle
```

```kotlin
toProduct(productId)
```

or navigation models like:

```kotlin
ProductDestination(productId)
```

These patterns indicate that the application is using structured navigation rather than manually constructing routes throughout the codebase.

---

# Pattern 1 — Passing Only What Is Needed

Production applications usually pass only the minimum information required to identify the destination.

For example,

```text
Product

↓

productId
```

instead of

```text
Entire Product Object
```

The destination can then load the remaining information from its Repository.

This keeps navigation lightweight and avoids transferring large amounts of data between screens.

---

# Pattern 2 — Strongly Typed Destinations

Rather than constructing routes manually,

many applications expose helper functions or destination models.

For example,

```kotlin
ProductDestination(productId)
```

or

```kotlin
navigator.openProduct(productId)
```

instead of

```kotlin
navController.navigate(
    "product/$productId"
)
```

This centralizes route construction and reduces duplication throughout the application.

---

# Pattern 3 — Optional Arguments

Some destinations support optional information.

For example,

```text
Search

↓

Query

↓

Optional Filter
```

The destination behaves correctly whether the optional value is supplied or not.

Keeping optional arguments explicit makes navigation easier to understand.

---

# Pattern 4 — Retrieving Arguments

Once navigation completes,

the destination retrieves its arguments.

In Compose applications,

this commonly happens through:

```kotlin
SavedStateHandle
```

or

```kotlin
backStackEntry.arguments
```

The destination uses those values to load the appropriate data.

Notice that the destination receives only the information needed to identify what should be displayed.

---

# Pattern 5 — Returning Results

Navigation isn't always one-way.

Sometimes a destination returns information before closing.

For example,

```text
Profile Editor

↓

Save

↓

Profile Updated
```

or

```text
Address Picker

↓

Address Selected
```

Rather than passing data only when navigating forward,

production applications also communicate results when returning.

---

# Pattern 6 — Type-Safe Navigation

As applications grow,

manually constructing route strings becomes difficult to maintain.

Production applications often introduce abstractions such as:

```kotlin
ProductDestination(...)
```

or

```kotlin
navigator.openCheckout(...)
```

These abstractions ensure every caller navigates consistently and reduce opportunities for mistakes.

---

# Pattern 7 — Keep Navigation Separate From Data

Navigation identifies **where** the user should go.

Repositories determine **what** data should be displayed.

For example,

```text
Navigate

↓

productId

↓

Product Screen

↓

Repository

↓

Product
```

This separation keeps navigation simple and avoids unnecessary coupling between destinations.

---

# How To Read Production Code

When you see:

```kotlin
navigator.openProduct(productId)
```

read it as:

```text
Navigate

To

Product

Using

Its Identifier
```

---

When you see:

```kotlin
SavedStateHandle
```

read it as:

```text
Retrieve

The Arguments

Used To Open

This Destination
```

---

When you see:

```kotlin
ProductDestination(...)
```

read it as:

```text
A Strongly Typed

Representation

Of A Destination
```

---

When you see:

```text
productId
```

instead of

```text
Product
```

read it as:

```text
Load The Data

Rather Than

Passing The Data
```

---

# Common Production Mistakes

## Passing Large Objects

Avoid passing entire models between destinations.

Instead, pass an identifier and allow the destination to load its own data.

This keeps navigation lightweight and avoids stale data.

---

## Constructing Routes Everywhere

Avoid manually building route strings throughout the application.

Centralize route creation behind destination models or navigator methods.

This reduces duplication and makes route changes easier.

---

## Mixing Navigation With Business Data

Navigation should identify the destination.

Business data should come from repositories or other data sources after navigation completes.

Keeping these responsibilities separate results in a cleaner architecture.

---

## Using Arguments As Shared State

Arguments describe how a destination was opened.

They should not become a replacement for shared application state.

If multiple destinations need the same mutable data, consider a shared ViewModel instead.

---

# Key Takeaways

```text
✓ Pass only the minimum information required for navigation.

✓ Prefer identifiers over entire objects.

✓ Retrieve navigation arguments inside the destination.

✓ Centralize route construction whenever possible.

✓ Navigation identifies where to go; repositories determine what to display.

✓ Keep navigation arguments separate from shared application state.
```