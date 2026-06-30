# Navigation With Compose

## Looking Back

In the previous chapters we learned:

```text
Navigation Graph = The Map

NavController = The Driver

Back Stack = The History
```

We saw NavHost and composable() in code examples,
but we have not yet examined how they work together.

A natural question appears:

```text
In Compose, How Do Screens Declare Themselves
As Navigation Destinations?
```

---

## The Core Idea

In Compose, screens are Composable functions.

There are no Fragments to swap.

There is no XML navigation graph.

Instead:

```text
Each Screen Is A Composable Function

Registered As A Destination

With A Route String
```

The navigation system decides
which Composable function to call
based on the current route.

---

## The Three Pieces

Compose Navigation has three key pieces:

```text
1. rememberNavController()

   Creates the driver

2. NavHost

   The container that displays
   the current destination

3. composable("route") { Screen() }

   Registers a screen
   as a destination
```

Together:

```text
rememberNavController()

↓

Creates The Driver

↓

NavHost

↓

Displays The Current Destination

↓

composable("route") { Screen() }

↓

Registers Screens As Destinations
```

---

## A Mental Model

Think of NavHost as a window.

Only one destination is visible at a time.

```text
┌─────────────────────┐
│                     │
│   NavHost Window    │
│                     │
│  ┌───────────────┐  │
│  │               │  │
│  │  Current      │  │
│  │  Destination  │  │
│  │               │  │
│  └───────────────┘  │
│                     │
└─────────────────────┘
```

When the NavController navigates to a new route,
NavHost swaps the content of the window.

```text
navController.navigate("cart")

↓

NavHost

↓

Stops Displaying Home

↓

Starts Displaying Cart
```

The window stays the same.

The content changes.

---

## Minimal Code

```kotlin
val navController = rememberNavController()

NavHost(
    navController = navController,
    startDestination = "home"
) {
    composable("home") { HomeScreen() }
    composable("cart") { CartScreen() }
}
```

Two destinations. One window. One driver.

---

## Production Code

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
                onRestaurantClick = { restaurantId ->
                    navController.navigate(
                        "restaurant/$restaurantId"
                    )
                }
            )
        }

        composable("restaurant/{id}") { backStackEntry ->
            val id = backStackEntry.arguments
                ?.getString("id") ?: return@composable

            RestaurantScreen(
                restaurantId = id,
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
                },
                onBack = {
                    navController.popBackStack()
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
                        popUpTo("home") { inclusive = true }
                    }
                }
            )
        }

    }
}
```

### How To Read It

```text
FoodDeliveryApp()

↓

The Top-Level Composable

↓

Creates The NavController

↓

Defines The Entire Navigation Graph

-------------------------

rememberNavController()

↓

Creates And Remembers The Driver

↓

Survives Recomposition (DOC 7)

-------------------------

NavHost(startDestination = "home")

↓

The Window That Displays
The Current Destination

↓

Starts By Showing Home

-------------------------

composable("restaurant/{id}") { backStackEntry -> }

↓

Destination With A Parameter

↓

"restaurant/42" → id = "42"

-------------------------

popUpTo("home")

↓

Clear The Back Stack Down To Home

↓

Prevents Going Back To Checkout After Order
```

---

## Where The NavController Lives

Notice that `rememberNavController()` is called
at the top of the Composable tree.

```text
FoodDeliveryApp             ← NavController lives here
  │
  └── NavHost
        │
        ├── HomeScreen      ← receives callbacks
        ├── RestaurantScreen ← receives callbacks
        ├── CartScreen      ← receives callbacks
        └── CheckoutScreen  ← receives callbacks
```

The NavController is hoisted state.

It lives at the top,
and navigation callbacks flow down.

> **You'll see this in...**
> - **DOC 9 — Production Compose Patterns**, where state hoisting is the core pattern for managing state ownership in Compose

Individual screens never create or own the NavController.

They receive navigation actions as callbacks.

This is the same pattern from DOC 5:

```text
Events Flow Up

↓

State Flows Down
```

---

## How Destinations Get Their ViewModels

Each `composable(...)` destination
has its own back stack entry.

That back stack entry can scope a ViewModel.

```text
composable("home")
  └── HomeViewModel (scoped to this entry)

composable("cart")
  └── CartViewModel (scoped to this entry)
```

When the user navigates away and the entry is popped,
the ViewModel is cleared.

When the user navigates back,
a fresh ViewModel is created.

```kotlin
composable("cart") {
    val viewModel: CartViewModel = hiltViewModel()
    CartScreen(viewModel = viewModel)
}
```

The ViewModel's lifetime matches the destination's lifetime
in the back stack.

---

## Production Notes

```text
• rememberNavController() creates a NavController
  that survives recomposition. It is hoisted state.

• NavHost is the container that displays
  one destination at a time.

• composable("route") registers a Composable function
  as a navigation destination.

• Route parameters like "product/{id}" allow passing
  simple data between destinations.

• popUpTo() controls what gets removed from the back stack
  when navigating. Use it to prevent the user
  from returning to screens that no longer make sense
  (like Checkout after an order is placed).

• Each destination can scope its own ViewModel
  using hiltViewModel() or viewModel().
  The ViewModel is automatically cleared
  when the destination is popped.
```

---

## Common Misconception

A common misconception is:

```text
NavHost Replaces The Activity
```

Incorrect.

NavHost is a Composable.

It lives inside the Activity's content.

```text
Activity

↓

setContent { }

↓

FoodDeliveryApp()

↓

NavHost

↓

Current Destination
```

The Activity still exists.

NavHost simply decides which Composable
to display inside it.

---

## A Natural Question

We can now define destinations,
navigate between them,
and manage the back stack.

But a question appears.

```text
Screens Need To Pass Data
To Each Other.

How Do Navigation Arguments Work?

And What About Complex Data
That Cannot Fit In A Route String?
```

That leads us to the next concept:

```text
Navigate And Arguments
```

---

## Revision

### Core Idea

```text
In Compose Navigation,
Each Screen Is A Composable Function
Registered As A Destination With A Route String
```

### Mental Model

```text
rememberNavController()

↓

Creates The Driver

↓

NavHost

↓

The Window

↓

composable("route") { Screen() }

↓

Registers Destinations
```

### Production Recognition

```kotlin
NavHost(
    navController = navController,
    startDestination = "home"
) {
    composable("home") { HomeScreen() }
    composable("cart") { CartScreen() }
}
```

↓

```text
Navigation Graph In Compose

↓

NavHost = Window

↓

composable() = Destination

↓

NavController = Driver
```

> **You'll see this in...**
> - **DOC 7 — Jetpack Compose**, where NavHost is itself a Composable that participates in the Composition Tree
> - **DOC 9 — Production Compose Patterns**, where NavController is hoisted to the top of the Composable tree as shared state
> - **DOC 14 — Dependency Injection**, where Hilt provides ViewModels scoped to navigation destinations via `hiltViewModel()`

### Previous Concept

```text
NavController
```

### Next Concept

```text
Navigate And Arguments
```
