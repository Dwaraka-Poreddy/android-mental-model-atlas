# Multi-Module Navigation — Production Code

## Purpose

Understand how production Android applications navigate between independent features without creating direct dependencies, and learn to recognize the architectural patterns commonly used in large codebases.

---

## Looking Back

In the previous chapter, we learned that large applications avoid direct feature-to-feature dependencies by communicating through navigation contracts.

This chapter focuses on how that idea is implemented in production Android applications.

---

# Recognition

When exploring a production Android project, you'll often notice that one feature never imports another feature's UI directly.

Instead, you'll commonly see patterns like:

```kotlin
navigator.openProfile(userId)
```

```kotlin
profileNavigator.navigate(userId)
```

```kotlin
navigationManager.navigate(ProfileDestination(userId))
```

or dedicated modules such as:

```text
:feature:shopping

:feature:profile

:feature:authentication

:navigation

:core:navigation
```

These are strong indicators that the project is using navigation contracts instead of direct feature dependencies.

---

# Pattern 1 — Direct Feature Navigation

Smaller applications often navigate directly.

```kotlin
navController.navigate("profile/$userId")
```

or

```kotlin
ProfileScreen(userId)
```

This works well for small projects.

However, every caller now knows how the Profile feature is implemented.

---

# Pattern 2 — Navigation Contracts

As applications grow, navigation is usually hidden behind a contract.

```kotlin
profileNavigator.openProfile(userId)
```

The caller no longer knows:

- the route,
- the destination,
- or the implementation details.

It simply requests:

```text
Open Profile
```

This keeps the caller independent of the Profile feature.

---

# Pattern 3 — Navigator Interfaces

A common production pattern is introducing an interface.

```kotlin
interface ProfileNavigator {

    fun openProfile(
        userId: String
    )
}
```

Features depend on the interface,

not the implementation.

Another module provides the concrete implementation.

This follows the Dependency Inversion Principle and keeps dependencies pointing in the correct direction.

---

# Pattern 4 — Central Navigation Manager

Some applications centralize navigation.

```kotlin
navigationManager.navigate(
    ProfileDestination(userId)
)
```

The navigation manager decides:

- which graph,
- which route,
- and which destination

should be opened.

Individual features never communicate directly.

---

# Pattern 5 — Feature APIs

Large applications often expose a small public navigation API instead of exposing internal screens.

For example,

```text
Profile Feature

↓

Public API

↓

Open Profile(userId)
```

The Profile feature is free to reorganize its screens,

rename routes,

or introduce nested graphs

without affecting callers.

---

# Pattern 6 — Dynamic Feature Modules

Very large Android applications sometimes use Dynamic Feature Modules.

For example,

```text
Base App

↓

Downloads

↓

Profile Feature
```

Navigation remains exactly the same.

The only difference is that the destination feature may be downloaded on demand before navigation continues.

---

# How To Read Production Code

When you see:

```kotlin
profileNavigator.openProfile(userId)
```

read it as:

```text
Navigate To The Profile Feature Without Knowing How It Is Implemented
```

---

When you see:

```kotlin
navigationManager.navigate(...)
```

read it as:

```text
Delegate Navigation To A Central Coordinator
```

---

When you see a dedicated navigation module such as:

```text
:core:navigation
```

read it as:

```text
Shared Navigation Infrastructure Used By Multiple Features
```

---

# Common Production Mistakes

## Features Importing Each Other

```text
Shopping

↓

Imports

↓

Profile
```

This creates tight coupling between features.

Prefer navigation contracts instead.

---

## Exposing Internal Routes

Avoid allowing other features to depend on route strings like:

```kotlin
"profile/{userId}"
```

Instead,

expose an intent or contract such as:

```kotlin
openProfile(userId)
```

The feature remains free to change its internal navigation.

---

## Sharing UI Instead Of Navigation

Features should communicate through navigation requests,

not by directly creating another feature's screens.

This preserves feature ownership and keeps responsibilities clear.

---

## Creating Circular Dependencies

Avoid situations like:

```text
Shopping

↓

Profile

↓

Orders

↓

Shopping
```

Circular dependencies make applications difficult to build,

test,

and evolve.

Navigation contracts help eliminate these dependency cycles.

---

# Key Takeaways

```text
✓ Large applications avoid direct feature-to-feature navigation.

✓ Navigation contracts hide implementation details.

✓ Navigator interfaces reduce coupling between features.

✓ Feature APIs expose navigation capabilities instead of internal screens.

✓ Dynamic Feature Modules extend the same principles to on-demand features.

✓ The goal is feature independence, not simply using multiple Gradle modules.
```
