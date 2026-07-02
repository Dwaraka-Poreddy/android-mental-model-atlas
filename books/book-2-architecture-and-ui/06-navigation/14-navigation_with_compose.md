<!--
Primary Question: How are all the navigation concepts implemented using Jetpack Compose?
Prerequisites: Production Navigation Patterns
After Reading: You can recognize and implement a complete production-ready navigation architecture using Navigation Compose.
-->

# Navigation with Compose

## Looking Back

Throughout this book, we've learned navigation one concept at a time.

We've learned:

- Why Navigation Exists
- Navigation Graph
- NavController
- NavHost
- Navigation & Arguments
- Back Stack
- Deep Links
- Type-Safe Navigation
- Nested Navigation Graphs
- Multi-Module Navigation
- Conditional Navigation
- ViewModel Scoping
- Production Navigation Patterns

A natural question appears.

```text
How Are All Of These Concepts Implemented Using Jetpack Compose?
```

This chapter answers that question.

Instead of introducing new concepts, we'll build a complete navigation system and connect every piece back to the concepts we've already learned.

---

# Step 1 — Creating The NavController

Every Navigation Compose application begins by creating a `NavController`.

```kotlin
val navController = rememberNavController()
```

You already know what a NavController is.

It is the object that:

- performs navigation,
- manages the back stack,
- and coordinates navigation throughout the application.

Compose simply creates it using:

```kotlin
rememberNavController()
```

The `remember` ensures the same NavController survives recompositions instead of being recreated.

> **We've already learned:** **NavController**, where we explored its responsibilities and lifecycle.

---

# Step 2 — Adding A NavHost

Next, the application needs somewhere to display destinations.

```kotlin
NavHost(
    navController = navController,
    startDestination = "home"
) {
}
```

The `NavHost` is the container responsible for displaying whichever destination is currently at the top of the back stack.

It doesn't perform navigation.

It simply displays the destination selected by the NavController.

> **We've already learned:** **NavHost**, where we explored why it exists and what it displays.

---

# Step 3 — Declaring Destinations

Inside the `NavHost`, destinations are declared.

```kotlin
NavHost(
    navController = navController,
    startDestination = "home"
) {

    composable("home") {
        HomeScreen()
    }

    composable("profile") {
        ProfileScreen()
    }

}
```

Each `composable(...)` call registers one destination in the navigation graph.

The route uniquely identifies that destination.

> **We've already learned:** **Navigation Graph** and **Navigation & Arguments**.

---

# Step 4 — Navigating

Moving between destinations is straightforward.

```kotlin
navController.navigate("profile")
```

Returning to the previous destination:

```kotlin
navController.popBackStack()
```

Behind the scenes:

`navigate()` pushes a destination onto the back stack,

while `popBackStack()` removes the current destination and reveals the previous one.

> **We've already learned:** **Back Stack**.

---

# Step 5 — Passing Arguments

Routes can contain parameters.

```kotlin
composable(
    route = "product/{id}"
)
```

Navigate using:

```kotlin
navController.navigate(
    "product/42"
)
```

The destination receives the value through its navigation arguments.

For more complex applications,

type-safe navigation avoids manually constructing route strings.

> **We've already learned:** **Navigation & Arguments** and **Type-Safe Navigation**.

---

# Step 6 — Nested Navigation Graphs

Large applications split navigation into smaller feature graphs.

Compose provides the `navigation()` builder.

```kotlin
navigation(
    route = "checkout_graph",
    startDestination = "shipping"
) {

    composable("shipping") { }

    composable("payment") { }

    composable("confirmation") { }

}
```

Each nested graph owns one feature or user flow.

> **We've already learned:** **Nested Navigation Graphs**.

---

# Step 7 — Deep Links

Compose allows destinations to be opened directly from external sources.

```kotlin
composable(
    route = "product/{id}",
    deepLinks = listOf(
        navDeepLink {
            uriPattern = "https://shop.com/product/{id}"
        }
    )
)
```

When a matching link is opened,

the navigation system navigates directly to that destination.

> **We've already learned:** **Deep Linking**.

---

# Step 8 — ViewModel Scoping

Most destinations obtain their ViewModel like this.

```kotlin
val viewModel: ProductViewModel =
    hiltViewModel()
```

This creates (or retrieves) the ViewModel scoped to the current destination.

For a graph-scoped ViewModel,

Compose retrieves the parent navigation graph.

```kotlin
val parentEntry = remember {
    navController.getBackStackEntry("checkout_graph")
}

val checkoutViewModel =
    hiltViewModel<CheckoutViewModel>(
        parentEntry
    )
```

Every destination inside the Checkout graph now shares the same ViewModel.

> **We've already learned:** **ViewModel Scoping**.

---

# Step 9 — A Complete Example

Let's bring everything together.

```kotlin
@Composable
fun FoodDeliveryApp() {

    val navController = rememberNavController()

    NavHost(
        navController = navController,
        startDestination = "home"
    ) {

        composable("home") {
            HomeScreen(
                onRestaurantClick = { id ->
                    navController.navigate("restaurant/$id")
                }
            )
        }

        composable("restaurant/{id}") {
            RestaurantScreen(
                onAddToCart = {
                    navController.navigate("cart")
                },
                onBack = {
                    navController.popBackStack()
                }
            )
        }

        composable("cart") {
            CartScreen(
                onCheckout = {
                    navController.navigate("checkout")
                }
            )
        }

        composable("checkout") {
            CheckoutScreen(
                onOrderPlaced = {
                    navController.navigate("confirmation") {
                        popUpTo("home")
                    }
                }
            )
        }

        composable("confirmation") {
            OrderConfirmationScreen(
                onBackToHome = {
                    navController.navigate("home") {
                        popUpTo("home") {
                            inclusive = true
                        }
                    }
                }
            )
        }

    }
}
```

### How To Read This

Don't read it as a wall of code.

Read it concept by concept.

```text
rememberNavController()

↓

Creates The NavController
```

---

```text
NavHost()

↓

Displays The Current Destination
```

---

```text
composable(...)

↓

Registers A Destination
```

---

```text
navigate(...)

↓

Pushes A Destination
Onto The Back Stack
```

---

```text
popBackStack()

↓

Returns To
The Previous Destination
```

---

```text
popUpTo(...)

↓

Removes Destinations

From The Back Stack

Before Navigating
```

Everything you've learned throughout this book appears inside this one example.

---

# Where The NavController Lives

Notice where the NavController is created.

```text
FoodDeliveryApp()

↓

NavController

↓

NavHost

↓

HomeScreen

RestaurantScreen

CartScreen

CheckoutScreen
```

The NavController lives at the top of the navigation hierarchy.

Individual screens don't create or own it.

Instead, they receive navigation callbacks from their parent.

This keeps screens reusable and independent of the navigation framework.

---

# The Big Picture

Let's revisit the complete navigation architecture.

```text
User Action

↓

Navigation Request

↓

Application State

↓

Navigate?

↓

NavController

↓

Navigation Graph

↓

Destination

↓

ViewModel

↓

Repository

↓

UI
```

Jetpack Compose isn't introducing new navigation concepts.

It provides an implementation of the architecture you've learned throughout this book.

---

## Revision

### Core Idea

```text
Navigation Compose

=

Jetpack Compose's Implementation

Of The Navigation Architecture

You've Learned
```

---

### Mental Model

```text
rememberNavController()

↓

NavHost

↓

Navigation Graph

↓

Destination

↓

ViewModel

↓

Repository

↓

UI
```

The framework provides the implementation.

The concepts remain the same.

---

### Production Recognition

When reading a Compose codebase, recognize the concepts behind the APIs:

- `rememberNavController()` → Navigation coordinator
- `NavHost()` → Navigation host
- `composable()` → Destination
- `navigate()` → Navigation request
- `navigation()` → Nested graph
- `hiltViewModel()` → ViewModel scope
- `popUpTo()` → Back stack management

Once you recognize these concepts, any Compose navigation codebase becomes much easier to understand.

---

# Completing The Journey

Congratulations.

You've completed the Navigation book of the Atlas.

You now understand:

- why navigation exists,
- how applications organize destinations,
- how navigation is performed,
- how arguments are passed,
- how back stack management works,
- how deep links enter the application,
- how large applications scale navigation,
- how features remain independent,
- how navigation decisions are made,
- how ViewModels are scoped,
- and how Jetpack Compose implements all of these concepts.

Navigation is no longer a collection of APIs.

It's a complete architectural system.

The next book of the Atlas builds on this foundation as we explore how Android applications build rich, responsive user interfaces using Jetpack Compose.
