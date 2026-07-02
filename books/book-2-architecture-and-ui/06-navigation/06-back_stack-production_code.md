# Back Stack — Production Code

## Purpose

Understand how Android applications manipulate the Back Stack in production code and learn to recognize common navigation patterns used in real-world codebases.

---

## Looking Back

In the previous chapter, we learned that the Back Stack is Android's navigation history.

Conceptually,

```text
Navigate

↓

Push Destination

↓

Back Stack

↓

Press Back

↓

Pop Destination
```

In production code,

we don't manipulate the Back Stack directly.

Instead,

we ask the `NavController` to perform operations on our behalf.

This chapter explores the APIs and patterns you'll encounter in real Android applications.

---

# Recognition

When exploring a production Android project,

you'll frequently encounter APIs such as:

```kotlin
navController.popBackStack()

navController.navigate(...)

popUpTo(...)

launchSingleTop = true

inclusive = true
```

All of these APIs manipulate the Back Stack in different ways.

---

# Pattern 1 — Normal Navigation

The most common navigation simply pushes a new destination onto the Back Stack.

```kotlin
navController.navigate("search")
```

Conceptually,

```text
Top

↓

Search

↓

Home
```

Every call to `navigate(...)` usually places another destination on top of the Back Stack.

---

# Pattern 2 — Going Back

Returning to the previous destination removes the current destination.

```kotlin
navController.popBackStack()
```

Conceptually,

Before:

```text
Top

↓

Cart

↓

Product

↓

Search

↓

Home
```

After:

```text
Top

↓

Product

↓

Search

↓

Home
```

This is exactly what happens when the user presses the system Back button.

---

# Pattern 3 — Removing Multiple Destinations

Sometimes returning only one screen isn't enough.

Imagine a login flow.

```text
Login

↓

Home
```

Once login succeeds,

the user should never return to Login.

Production applications remove it from the Back Stack.

```kotlin
navController.navigate("home") {
    popUpTo("login")
}
```

Conceptually,

Before:

```text
Top

↓

Home

↓

Login
```

After:

```text
Top

↓

Home
```

---

# Pattern 4 — Inclusive Removal

Sometimes we also want to remove the destination passed to `popUpTo()`.

```kotlin
navController.navigate("home") {
    popUpTo("login") {
        inclusive = true
    }
}
```

Conceptually,

Without `inclusive`:

```text
Home

↓

Login
```

With `inclusive = true`:

```text
Home
```

The specified destination is removed as well.

---

# Pattern 5 — Avoiding Duplicate Destinations

Imagine repeatedly tapping the same navigation button.

Without protection,

the Back Stack might become:

```text
Home

↓

Search

↓

Search

↓

Search
```

Production applications often prevent this.

```kotlin
navController.navigate("search") {
    launchSingleTop = true
}
```

If Search is already the current destination,

another instance won't be added.

---

# Pattern 6 — Clearing The Entire Flow

Some user actions completely change the application.

Examples include:

- Logout
- Account deletion
- Checkout completed
- Onboarding finished

The previous flow should disappear.

Conceptually,

```text
Checkout

↓

Payment

↓

Cart

↓

Home
```

becomes

```text
Login
```

Production applications accomplish this by combining navigation with Back Stack operations.

---

# Multiple Back Stacks

Modern applications often have multiple independent navigation flows.

A common example is Bottom Navigation.

```text
Home Tab

↓

Home

↓

Search

-------------------------

Profile Tab

↓

Profile

↓

Edit Profile
```

Each tab maintains its own Back Stack.

Switching tabs restores the previous navigation history for that tab.

We'll study this architecture in more detail when we cover Bottom Navigation and Nested Navigation Graphs.

---

# How To Read Production Code

When you encounter:

```kotlin
navController.popBackStack()
```

Read it as:

```text
Remove The Current Destination And Reveal The Previous One
```

---

When you encounter:

```kotlin
popUpTo(...)
```

Read it as:

```text
Remove Part Of The Navigation History
```

---

When you encounter:

```kotlin
inclusive = true
```

Read it as:

```text
Also Remove The Specified Destination
```

---

When you encounter:

```kotlin
launchSingleTop = true
```

Read it as:

```text
Avoid Creating Duplicate Destinations
```

---

# Common Production Mistakes

## Creating Duplicate Screens

```kotlin
navController.navigate("search")
navController.navigate("search")
navController.navigate("search")
```

The Back Stack grows unnecessarily.

Use `launchSingleTop` when appropriate.

---

## Forgetting To Clear Authentication Screens

After login,

users shouldn't navigate back to the Login screen.

Use `popUpTo()` to remove obsolete destinations.

---

## Treating The Back Stack Like The Navigation Graph

The Navigation Graph defines all possible destinations.

The Back Stack only remembers the user's actual journey.

Never confuse these two concepts.

---

# Key Takeaways

```text
✓ navigate() usually pushes a destination.

✓ popBackStack() removes the current destination.

✓ popUpTo() removes part of the navigation history.

✓ inclusive = true also removes the specified destination.

✓ launchSingleTop prevents duplicate destinations.

✓ Modern apps may maintain multiple Back Stacks.

✓ The Back Stack changes continuously as users navigate.
```