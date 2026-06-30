# Navigation Graph

## Looking Back

In the previous chapter we learned:

```text
Navigation = A System That Manages

↓

Which Screen Is Visible

↓

How Screens Connect

↓

Screen History (Back Stack)
```

A natural question appeared:

```text
How Does The System Know
Which Screens Exist
And How They Connect?
```

---

## The Problem

Suppose you are building an e-commerce app.

The app has these screens:

```text
Home
Search
Product Details
Cart
Checkout
Order Confirmation
```

Some transitions make sense:

```text
Home → Search           ✓
Product Details → Cart  ✓
Cart → Checkout         ✓
```

Some transitions do not:

```text
Checkout → Search       ✗
Order Confirmation → Cart  ✗
```

Without a clear definition of what is allowed,
any screen could navigate to any other screen.

The system needs a blueprint.

---

## Navigation Graph

A Navigation Graph is:

```text
The Blueprint Of All Possible
Screen Transitions In The App
```

It defines two things:

```text
1. Every destination the user can visit

2. Every transition that connects them
```

Think of it as a map the navigation system follows.

---

## A Mental Model

Consider the e-commerce app.

```text
   [Home]
    |   \
    ↓    ↓
[Search] [Profile]
    |
    ↓
[Product Details]
    |
    ↓
  [Cart]
    |
    ↓
[Checkout]
    |
    ↓
[Order Confirmation]
```

Each box is a destination.

Each arrow is a route the user can take.

Together, they form the navigation graph.

---

## Destinations and Routes

The graph consists of two things.

Destinations:

```text
Destination = A Screen The User Can Visit
```

Routes:

```text
Route = A Path From One Destination To Another
```

Every destination has a unique identifier.

```text
"home"
"search"
"product/{id}"
"cart"
"checkout"
```

The navigation system uses these identifiers
to know which screen to display.

---

## Start Destination

Every navigation graph has a start destination.

```text
Navigation Graph

↓

Start Destination = Home

↓

Home → Search → Product Details → Cart → ...
```

When the app opens,
the navigation system displays the start destination.

If the back stack is empty
and the user presses Back,
the app exits.

---

## Minimal Code

```kotlin
NavHost(startDestination = "home") {

    composable("home") { HomeScreen() }

    composable("search") { SearchScreen() }

    composable("product/{id}") { ProductScreen() }

    composable("cart") { CartScreen() }
}
```

This declares four destinations
with the start destination set to "home".

---

## Production Code

```kotlin
@Composable
fun AppNavigation() {

    val navController = rememberNavController()

    NavHost(
        navController = navController,
        startDestination = "home"
    ) {

        composable("home") {
            HomeScreen(
                onSearchClick = {
                    navController.navigate("search")
                },
                onCartClick = {
                    navController.navigate("cart")
                }
            )
        }

        composable("search") {
            SearchScreen(
                onProductClick = { productId ->
                    navController.navigate("product/$productId")
                }
            )
        }

        composable("product/{id}") {
            ProductScreen()
        }

        composable("cart") {
            CartScreen()
        }

    }
}
```

### How To Read It

Don't read this as a wall of code.

Read it as a map:

```text
AppNavigation

↓

Declares The Map

-------------------------

NavHost

↓

The Container That Holds The Map

↓

startDestination = "home"

↓

App Opens On Home

-------------------------

composable("home") { HomeScreen() }

↓

"home" Is A Destination

↓

HomeScreen Is What Gets Displayed

-------------------------

navController.navigate("search")

↓

Follow The Route To "search"

-------------------------

composable("product/{id}")

↓

"product/{id}" Is A Destination

↓

{id} Is A Placeholder For Data
```

Every `composable(...)` block adds a destination to the map.

Every `navigate(...)` call follows a route on that map.

> **You'll see this in...**
> - **DOC 5 — ViewModel**, where each destination gets its own ViewModel scoped to that destination's back stack entry

---

## How The Graph Connects To Architecture

Each destination in the graph
can have its own ViewModel.

```text
composable("home")
  └── HomeViewModel

composable("search")
  └── SearchViewModel

composable("product/{id}")
  └── ProductViewModel

composable("cart")
  └── CartViewModel
```

The navigation graph defines the destinations.

The ViewModel owns the state for each destination.

```text
Navigation Graph

↓

Defines Where

-------------------------

ViewModel

↓

Defines What State
```

---

## Production Notes

```text
• The navigation graph is the single source of truth
  for all possible screen transitions.

• Each destination is identified by a unique route string.

• The start destination is the first screen the user sees.

• Route strings can contain parameters like "product/{id}"
  for passing data between screens.

• Defining the graph in one place makes it easy to understand
  all possible paths through the app.

• The navigation graph does not decide when to navigate.
  It only defines where navigation can go.
```

---

## Common Misconception

A common misconception is:

```text
Navigation Graph = A List Of Screens
```

Incorrect.

A list of screens has no connections.

```text
List

↓

Home, Search, Cart, Checkout

-------------------------

Graph

↓

Home → Search → Product → Cart → Checkout
```

The graph defines both the destinations
and the transitions between them.

Without transitions, the system has no idea
which screens can reach which other screens.

---

## A Natural Question

We now have a map of all screens and their connections.

But a question immediately appears.

```text
The Map Exists.

Who Actually Reads The Map
And Drives The Navigation?
```

That leads us to the next concept:

```text
NavController
```

---

## Revision

### Core Idea

```text
Navigation Graph = The Blueprint Of All Possible
Screen Transitions In The App
```

### Mental Model

```text
   [Home]
    |   \
    ↓    ↓
[Search] [Profile]
    |
    ↓
[Product Details]
    |
    ↓
  [Cart]

Destinations = Nodes

Transitions = Edges
```

### Production Recognition

```kotlin
NavHost(startDestination = "home") {
    composable("home") { ... }
    composable("search") { ... }
}
```

↓

```text
Navigation Graph

↓

Destinations + Routes + Start
```

> **You'll see this in...**
> - **DOC 7 — Jetpack Compose**, where each destination renders a Composable function
> - **DOC 14 — Dependency Injection**, where Hilt provides ViewModels scoped to navigation destinations
> - **DOC 16 — Testing Strategy**, where navigation graphs are tested to verify correct screen transitions

### Previous Concept

```text
Why Navigation Exists
```

### Next Concept

```text
NavController
```
