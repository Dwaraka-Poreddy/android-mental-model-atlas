# ViewModel Scoping — Production Code

## Purpose

Understand how Android applications scope ViewModels to different lifecycles and learn to recognize the ViewModel scoping patterns used in production codebases.

---

## Looking Back

In the previous chapter, we learned that a ViewModel belongs to a scope rather than a screen.

This chapter focuses on how Android implements those scopes using Jetpack Navigation, Hilt, and the ViewModel framework.

---

# Recognition

When exploring a production Android project, you'll commonly encounter code such as:

```kotlin
hiltViewModel()
```

```kotlin
hiltViewModel(navBackStackEntry)
```

```kotlin
activityViewModels()
```

```kotlin
navController.getBackStackEntry(...)
```

```kotlin
SavedStateHandle
```

These are strong indicators that the application is controlling **how long a ViewModel should live** and **which destinations should share it**.

---

# Pattern 1 — Destination Scoped ViewModel

The most common scope is a single destination.

```kotlin
@Composable
fun ProductScreen(
    viewModel: ProductViewModel = hiltViewModel()
) {
    ...
}
```

Every destination receives its own ViewModel.

When the destination is removed from the navigation back stack, the ViewModel is destroyed automatically.

This is the default behavior in most Compose applications.

---

# Pattern 2 — Graph Scoped ViewModel

Sometimes multiple destinations belong to the same user flow.

For example,

```text
Checkout

↓

Shipping

↓

Payment

↓

Confirmation
```

Rather than creating three separate ViewModels,

the entire Checkout graph shares one.

```kotlin
val parentEntry = remember {
    navController.getBackStackEntry("checkout_graph")
}

val viewModel: CheckoutViewModel =
    hiltViewModel(parentEntry)
```

As long as the Checkout graph remains on the back stack, every destination inside that graph receives the same ViewModel instance.

The ViewModel is destroyed only when the entire graph finishes.

---

# Pattern 3 — Activity Scoped ViewModel

Some state must be shared across many destinations.

For example,

- current user,
- theme,
- app-wide search,
- shared toolbar state.

In these cases,

the Activity owns the ViewModel.

```kotlin
private val viewModel by activityViewModels<AppViewModel>()
```

Every destination hosted by the Activity accesses the same instance.

The ViewModel survives until the Activity itself is destroyed.

---

# Pattern 4 — SavedStateHandle

Navigation arguments are commonly exposed through `SavedStateHandle`.

```kotlin
@HiltViewModel
class ProductViewModel(
    savedStateHandle: SavedStateHandle
) : ViewModel() {

    val productId: String =
        checkNotNull(savedStateHandle["productId"])
}
```

Notice that `SavedStateHandle` **does not define the scope**.

Its responsibility is to expose navigation arguments and preserve small pieces of state while the ViewModel is alive.

> **Already covered:** **DOC 1 — ViewModel → SavedStateHandle**, where we explored state restoration in detail. Here we're focusing only on its role within navigation.

---

# Pattern 5 — Sharing State Across Destinations

A common production requirement is sharing state across several screens in the same flow.

For example,

```text
Shipping

↓

Payment

↓

Confirmation
```

Every screen edits the same checkout information.

Instead of passing partial state between destinations,

all screens share one graph-scoped ViewModel.

This keeps the flow simple and ensures every destination works with the same source of truth.

---

# Choosing The Right Scope

| Situation | Recommended Scope |
|-----------|-------------------|
| Independent screen | Destination |
| Multi-step flow | Navigation Graph |
| Shared app state | Activity |

The chosen scope should match how long the state needs to remain alive.

---

# How To Read Production Code

When you see:

```kotlin
hiltViewModel()
```

read it as:

```text
Create Or Retrieve The ViewModel Owned By This Destination
```

---

When you see:

```kotlin
hiltViewModel(parentEntry)
```

read it as:

```text
Retrieve The ViewModel Owned By This Navigation Graph
```

---

When you see:

```kotlin
activityViewModels()
```

read it as:

```text
Retrieve The ViewModel Owned By The Activity
```

---

When you see:

```kotlin
SavedStateHandle
```

read it as:

```text
Access Navigation Arguments And Small Restorable State
```

---

# Common Production Mistakes

## Sharing A ViewModel Unnecessarily

Not every group of screens should share one ViewModel.

If the state belongs to a single destination, keep the ViewModel destination-scoped.

---

## Using Activity Scope For Everything

Activity-scoped ViewModels live much longer than destination-scoped ViewModels.

Using them unnecessarily can cause unrelated screens to share state and increase memory usage.

Choose the smallest scope that satisfies the requirement.

---

## Passing State Between Screens Manually

Avoid copying state from one destination to another when both belong to the same flow.

A graph-scoped ViewModel usually provides a cleaner solution.

---

## Confusing Scope With Lifetime

A ViewModel's lifetime isn't determined by where it is declared.

It's determined by the scope that owns it.

Understanding the owner explains when the ViewModel is created and when it is destroyed.

---

# Key Takeaways

```text
✓ Android provides multiple ViewModel scopes.

✓ Destination-scoped ViewModels are the default for most screens.

✓ Navigation graph-scoped ViewModels allow multiple destinations to share state.

✓ Activity-scoped ViewModels share state across the entire Activity.

✓ SavedStateHandle exposes navigation arguments and preserves small amounts of state.

✓ Always choose the smallest scope that matches the required lifetime.
```