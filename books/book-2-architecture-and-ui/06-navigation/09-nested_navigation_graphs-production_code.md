# Nested Navigation Graphs — Production Code

## Purpose

Understand how Android applications implement nested navigation graphs and learn to recognize the navigation architecture used in medium and large production codebases.

---

## Looking Back

In the previous chapter, we learned that nested navigation graphs help organize large applications into smaller, feature-specific navigation graphs.

This chapter focuses on how that organization appears in production Android projects.

---

# Recognition

One of the easiest ways to recognize nested navigation graphs in a production codebase is that the application's navigation is divided into multiple feature-specific graph definitions rather than one large `NavHost`.

For example, you'll often see code like:

```kotlin
navigation(
    route = "auth_graph",
    startDestination = "login"
) {
    ...
}

navigation(
    route = "shopping_graph",
    startDestination = "home"
) {
    ...
}

navigation(
    route = "profile_graph",
    startDestination = "profile"
) {
    ...
}
```

or multiple graph files such as:

```text
auth_navigation.kt

shopping_navigation.kt

profile_navigation.kt
```

These are strong indicators that the application has organized its navigation into feature-specific graphs instead of maintaining one large navigation graph.

---

# Pattern 1 — A Single Navigation Graph

Small applications often begin with one graph.

```text
App

↓

Navigation Graph

↓

Home

Search

Product

Cart
```

This works well while the application remains small.

---

# Pattern 2 — Splitting By Feature

As the application grows,

navigation is grouped by feature instead of placing every destination into one graph.

```text
Application

├── Authentication Graph
│   ├── Login
│   ├── Register
│   └── Forgot Password
│
├── Shopping Graph
│   ├── Home
│   ├── Search
│   ├── Product Details
│   ├── Cart
│   └── Checkout
│
└── Profile Graph
    ├── Profile
    ├── Edit Profile
    └── Settings
```

Each graph owns a single feature.

---

# Pattern 3 — Defining Nested Graphs

Compose Navigation provides the `navigation()` builder for creating nested graphs.

```kotlin
NavHost(
    navController = navController,
    startDestination = "auth_graph"
) {

    navigation(
        route = "auth_graph",
        startDestination = "login"
    ) {
        composable("login") { ... }
        composable("register") { ... }
    }

    navigation(
        route = "shopping_graph",
        startDestination = "home"
    ) {
        composable("home") { ... }
        composable("search") { ... }
        composable("product/{id}") { ... }
    }
}
```

Notice that each feature declares its own destinations independently.

---

# Pattern 4 — Feature Ownership

In production applications,

each feature usually owns its own navigation graph.

For example,

```text
Authentication Team

↓

Authentication Graph

-------------------------

Shopping Team

↓

Shopping Graph

-------------------------

Profile Team

↓

Profile Graph
```

This reduces conflicts between teams and makes navigation easier to evolve.

---

# Pattern 5 — Authentication Flow

Authentication is one of the most common nested graphs.

```text
Authentication Graph

↓

Splash

↓

Login

↓

Register

↓

Forgot Password
```

Once authentication succeeds,

the application leaves the Authentication Graph and enters the main application graph.

---

# Pattern 6 — Bottom Navigation

Bottom Navigation often combines several nested graphs.

```text
Bottom Navigation

├── Home Graph
├── Search Graph
├── Orders Graph
└── Profile Graph
```

Each tab maintains its own navigation flow while remaining part of the overall application.

---

# Pattern 7 — Onboarding Flow

Another common example is onboarding.

```text
Onboarding Graph

↓

Welcome

↓

Permissions

↓

Preferences

↓

Complete
```

Once onboarding finishes,

users never return to this graph during normal application usage.

---

# How To Read Production Code

When you see:

```kotlin
navigation(
    route = "shopping_graph"
)
```

read it as:

```text
Everything inside this block belongs to the Shopping feature.
```

---

When you see:

```kotlin
startDestination = "login"
```

read it as:

```text
This is the first destination inside this feature graph.
```

---

When you see multiple `navigation()` blocks, read them as:

```text
Independent Feature Graphs

↓

Combined Into One Application
```

---

# Common Production Mistakes

## One Massive Navigation Graph

Placing every destination into a single graph eventually makes navigation difficult to understand and maintain.

Split graphs as features become independent.

---

## Splitting Too Early

Very small applications often don't need nested graphs.

Introducing them too early can add unnecessary complexity.

Grow the navigation structure as the application grows.

---

## Mixing Unrelated Features

Avoid grouping unrelated destinations into the same graph.

For example,

```text
Authentication

+

Shopping

+

Profile
```

inside one feature graph makes ownership unclear.

Instead, each graph should represent a single business capability.

---

## Using Nested Graphs Instead Of Feature Boundaries

Nested graphs improve navigation organization.

They do not automatically create architectural boundaries.

Feature ownership, dependency management, and module boundaries are separate concerns.

---

# Key Takeaways

```text
✓ Nested graphs organize large navigation structures.

✓ Each graph usually represents one feature.

✓ Compose Navigation provides the navigation() builder for nested graphs.

✓ Authentication, onboarding, and bottom navigation commonly use nested graphs.

✓ Nested graphs improve maintainability without changing navigation behavior.

✓ They organize navigation—they don't replace architectural boundaries.
```