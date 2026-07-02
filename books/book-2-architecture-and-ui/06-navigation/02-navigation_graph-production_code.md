# Navigation Graph — Production Code

## Purpose

Understand how production Android applications organize navigation graphs and learn to recognize the navigation graph structures commonly used in real codebases.

---

## Looking Back

In the previous chapter, we learned that a Navigation Graph is the blueprint that defines every destination and how they connect.

This chapter focuses on how that blueprint appears in production Android applications.

---

# Recognition

When exploring a production Android project, you'll rarely find one enormous navigation graph containing every destination.

Instead, you'll commonly see navigation organized into feature-specific graphs.

For example,

```text
auth_navigation.kt

shopping_navigation.kt

profile_navigation.kt

settings_navigation.kt
```

or

```text
NavHost

├── Authentication Graph
├── Shopping Graph
├── Profile Graph
└── Settings Graph
```

These are strong indicators that the application has organized its navigation around features rather than maintaining one large graph.

---

# Pattern 1 — Small Applications

Small applications often define every destination in a single navigation graph.

```kotlin
NavHost(
    navController = navController,
    startDestination = "home"
) {

    composable("home") { }

    composable("search") { }

    composable("cart") { }

}
```

This approach is simple and easy to understand while the application contains only a few destinations.

---

# Pattern 2 — Feature-Based Organization

As applications grow, destinations are grouped by feature.

```text
Application

├── Authentication
│   ├── Login
│   ├── Register
│   └── Forgot Password
│
├── Shopping
│   ├── Home
│   ├── Search
│   ├── Product
│   └── Checkout
│
└── Profile
    ├── Profile
    ├── Edit Profile
    └── Settings
```

Each feature owns its own section of the navigation graph.

This makes navigation easier to understand and maintain.

---

# Pattern 3 — Extracting Graphs Into Separate Files

Rather than placing every destination inside one file,

production projects often separate navigation by feature.

```text
navigation/

├── auth_navigation.kt
├── shopping_navigation.kt
├── profile_navigation.kt
└── settings_navigation.kt
```

Each file contributes destinations to the application's overall navigation graph.

This keeps individual navigation files small and focused.

---

# Pattern 4 — Meaningful Route Names

Production applications usually choose routes that describe the feature rather than the UI implementation.

For example,

```text
"profile"

"checkout"

"orders"

"settings"
```

instead of

```text
"screen1"

"page2"

"fragmentA"
```

Clear route names make navigation easier to understand throughout the codebase.

---

# Pattern 5 — One Start Destination

Every navigation graph defines exactly one start destination.

```kotlin
NavHost(
    startDestination = "home"
)
```

When reading production code, the start destination immediately tells you where the user enters that graph.

For nested graphs, each graph defines its own starting destination.

---

# Pattern 6 — Navigation Graphs Describe Structure

A navigation graph defines:

- what destinations exist,
- how they connect,
- and where navigation begins.

It does **not** decide:

- when navigation happens,
- whether navigation should happen,
- or who requests navigation.

Those responsibilities belong elsewhere in the navigation architecture.

---

# How To Read Production Code

When you see:

```kotlin
NavHost(
    startDestination = "home"
)
```

read it as:

```text
This Defines

The Root Navigation Graph
```

---

When you see:

```kotlin
navigation(
    route = "checkout_graph"
)
```

read it as:

```text
Everything Inside

Belongs To

The Checkout Feature
```

---

When you see multiple navigation files such as:

```text
auth_navigation.kt

shopping_navigation.kt

profile_navigation.kt
```

read them as:

```text
Independent Feature Graphs

Combined Into

One Application
```

---

# Common Production Mistakes

## One Massive Navigation Graph

Avoid placing every destination into one enormous graph.

As the application grows, the graph becomes difficult to understand and maintain.

Group related destinations into feature-specific graphs instead.

---

## Organizing By UI Instead Of Feature

Avoid grouping destinations simply because they look similar.

Instead, group them by the business capability they represent.

For example,

```text
Shopping

↓

Search

↓

Product

↓

Cart

↓

Checkout
```

is a stronger organization than grouping screens based on visual similarity.

---

## Poor Route Names

Avoid routes like:

```text
screen1

page2

test
```

Use descriptive routes that communicate intent.

For example,

```text
profile

orders

checkout

settings
```

---

## Mixing Navigation Decisions Into The Graph

The navigation graph should describe structure.

It shouldn't contain business decisions such as authentication checks or feature flag logic.

Keep the graph declarative.

Place navigation decisions elsewhere.

---

# Key Takeaways

```text
✓ Production applications organize navigation graphs by feature.

✓ Large applications split graphs across multiple files.

✓ Every graph has one clear start destination.

✓ Route names should describe business concepts rather than UI implementation.

✓ Navigation graphs describe application structure, not navigation decisions.

✓ A well-organized navigation graph becomes the blueprint for the entire application.
```