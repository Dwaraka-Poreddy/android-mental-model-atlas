# NavHost — Production Code

## Purpose

Understand how production Android applications organize the NavHost and learn to recognize the architectural patterns commonly used around it.

---

## Looking Back

In the previous chapter, we learned that the NavHost displays the current destination selected by the NavController.

This chapter focuses on how production Android applications organize the NavHost as the central entry point for their navigation architecture.

---

# Recognition

When exploring a production Android project, you'll commonly encounter code such as:

```kotlin
NavHost(...)
```

or dedicated files like:

```text
AppNavigation.kt

Navigation.kt

MainNavHost.kt
```

These files usually contain the application's root navigation graph.

Rather than rendering UI directly, they define how destinations, graphs, and navigation flows are connected.

---

# Pattern 1 — A Single Root NavHost

Most Compose applications have one root `NavHost`.

```kotlin
NavHost(
    navController = navController,
    startDestination = "home"
) {
    ...
}
```

Every destination in the application is ultimately declared inside this navigation hierarchy.

The root NavHost becomes the application's primary navigation container.

---

# Pattern 2 — NavHost Near The Application Root

Production applications usually place the NavHost near the top of the UI hierarchy.

```kotlin
App()

↓

NavHost()

↓

Destinations
```

This allows every screen in the application to participate in the same navigation system.

Keeping the NavHost near the root also avoids creating multiple independent navigation hierarchies unintentionally.

---

# Pattern 3 — Delegating To Feature Graphs

As applications grow, the NavHost rarely contains every destination directly.

Instead, it delegates to feature-specific navigation graphs.

```kotlin
NavHost()

├── authGraph()

├── shoppingGraph()

├── profileGraph()

└── settingsGraph()
```

Each feature contributes its own destinations while the NavHost remains clean and easy to understand.

---

# Pattern 4 — One Responsibility

The NavHost has one responsibility:

```text
Display

The Destination

Selected By

The NavController
```

It should not contain:

- authentication logic,
- business rules,
- repository calls,
- or feature-specific decisions.

Those responsibilities belong elsewhere.

---

# Pattern 5 — The Application Entry Point

When developers want to understand an application's navigation,

one of the first files they open is usually the NavHost.

From there they can quickly identify:

- the start destination,
- the available feature graphs,
- and the overall navigation structure.

For this reason, production teams often keep the NavHost intentionally small and easy to read.

---

# How To Read Production Code

When you see:

```kotlin
NavHost(...)
```

read it as:

```text
The Root

Of The Navigation System
```

---

When you see:

```kotlin
startDestination = ...
```

read it as:

```text
The First Destination

In This Navigation Graph
```

---

When you see:

```kotlin
authGraph()

shoppingGraph()

profileGraph()
```

read it as:

```text
Independent Features

Contributing

To One Navigation System
```

---

# Common Production Mistakes

## Making The NavHost Too Large

Avoid declaring dozens of destinations directly inside one NavHost.

Delegate feature-specific destinations to their own navigation graphs.

This keeps the navigation hierarchy easier to understand.

---

## Mixing Business Logic Into The NavHost

The NavHost should describe navigation structure.

Avoid placing authentication checks, feature flag logic, or repository calls inside it.

Those decisions belong to other architectural layers.

---

## Creating Multiple Root NavHosts

Most applications require only one root NavHost.

Creating multiple independent root hosts unnecessarily makes navigation harder to reason about.

Use nested navigation graphs when organizing large applications.

---

## Treating NavHost As A Screen

The NavHost isn't another screen in the application.

It is the container responsible for displaying whichever destination the NavController selects.

Keeping this distinction clear makes the overall architecture much easier to understand.

---

# Key Takeaways

```text
✓ Production applications usually have one root NavHost.

✓ The NavHost lives near the top of the UI hierarchy.

✓ Large applications delegate destinations to feature-specific navigation graphs.

✓ The NavHost defines navigation structure, not business logic.

✓ A well-organized NavHost provides a clear overview of the application's navigation architecture.

✓ The NavHost is the entry point to understanding an application's navigation system.
```