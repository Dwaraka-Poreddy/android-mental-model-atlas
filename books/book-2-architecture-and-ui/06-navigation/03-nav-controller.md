# NavController

## Looking Back

In the previous chapter we learned:

```text
Navigation Graph = The Blueprint Of All Possible
Screen Transitions In The App
```

The map exists.

But a natural question appears:

```text
Who Actually Reads The Map
And Drives The Navigation?
```

---

## The Problem

Suppose the user is on the Home screen
and taps "Search."

Several things need to happen:

```text
1. Look up "search" in the navigation graph

2. Create the Search screen

3. Push it onto the back stack

4. Display it
```

And when the user presses Back:

```text
1. Remove the top screen from the back stack

2. Display the screen beneath it
```

The navigation graph cannot do this.

It is a map, not a driver.

Something needs to read the map
and execute the transitions.

---

## NavController

The NavController is:

```text
The Driver That Follows The Navigation Graph
And Manages The Back Stack
```

It has two responsibilities:

```text
1. Execute navigation commands (go forward, go back)

2. Maintain the back stack (track screen history)
```

Think of it this way:

```text
Navigation Graph = The Map

NavController = The Driver
```

The map tells the driver where roads exist.

The driver decides when to move
and remembers where it has been.

---

## A Mental Model

```text
User Taps "Search"

↓

NavController

↓

Looks Up "search" In The Graph

↓

Pushes Search Onto The Back Stack

↓

Search Screen Becomes Visible
```

And when the user presses Back:

```text
User Presses Back

↓

NavController

↓

Pops Search From The Back Stack

↓

Home Screen Becomes Visible Again
```

The NavController is the single point of control
for all navigation in the app.

---

## The Key Operations

The NavController exposes two fundamental operations.

Navigate forward:

```text
navController.navigate("search")

↓

Push "search" Onto The Back Stack

↓

Display Search Screen
```

Navigate back:

```text
navController.popBackStack()

↓

Pop Top Screen From The Back Stack

↓

Display Previous Screen
```

Every navigation action in the app
goes through the NavController.

---

## Minimal Code

```kotlin
// Navigate forward
navController.navigate("search")

// Navigate back
navController.popBackStack()

// Check current destination
navController.currentBackStackEntry
```

Three operations cover most navigation needs.

---

## Production Code

```kotlin
@Composable
fun HomeScreen(
    onSearchClick: () -> Unit,
    onCartClick: () -> Unit
) {
    Column {

        SearchBar(
            onClick = onSearchClick
        )

        ProductGrid()

        CartButton(
            onClick = onCartClick
        )

    }
}

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
                },
                onBack = {
                    navController.popBackStack()
                }
            )
        }

        composable("cart") {
            CartScreen()
        }

    }
}
```

### How To Read It

```text
HomeScreen

↓

Does Not Know About NavController

↓

Receives Navigation As Callbacks

↓

onSearchClick, onCartClick

-------------------------

AppNavigation

↓

Creates NavController

↓

Wires Callbacks To Navigate Calls

-------------------------

navController.navigate("search")

↓

Driver Follows The Route

↓

Search Screen Appears

-------------------------

navController.popBackStack()

↓

Driver Goes Back

↓

Previous Screen Appears
```

Notice that HomeScreen never calls `navController.navigate(...)` directly.

It receives lambda callbacks.

The parent wires those callbacks to the NavController.

This keeps screens independent of the navigation system.

---

## Navigation Events From ViewModel

Sometimes navigation needs to happen
after a business operation completes.

For example, after placing an order:

```text
User Taps "Place Order"

↓

ViewModel

↓

Repository.placeOrder()

↓

Order Confirmed

↓

Navigate To Order Confirmation
```

> **You'll see this in...**
> - **DOC 4 — Channel**, where one-time navigation events are sent from ViewModel to UI using Channel, ensuring the event is consumed exactly once

The ViewModel decides when to navigate.

The UI executes the navigation.

```text
ViewModel

↓

Sends Navigation Event

↓

UI

↓

navController.navigate("order-confirmation")
```

This follows the same Unidirectional Data Flow
from DOC 5.

Events flow from UI to ViewModel.

Navigation commands flow from ViewModel back to UI.

---

## Production Notes

```text
• NavController is the single point of control
  for all navigation in the app.

• navigate() pushes a destination onto the back stack.
  popBackStack() removes the top destination.

• Screens should not hold a direct reference
  to the NavController. Pass navigation actions
  as lambda callbacks instead.

• Navigation events from ViewModels are typically
  sent using Channel (DOC 4) to ensure
  each event is consumed exactly once.

• The NavController automatically handles
  the system Back button.

• There is usually one NavController per navigation graph.
  Nested navigation graphs may have their own.
```

---

## Common Misconception

A common misconception is:

```text
Every Screen Should Call navController.navigate() Directly
```

Incorrect.

If every screen holds a reference to the NavController,
screens become coupled to the navigation system.

```text
Coupled

↓

SearchScreen(navController)

↓

navController.navigate("product/$id")

-------------------------

Decoupled

↓

SearchScreen(onProductClick: (String) -> Unit)

↓

onProductClick(id)
```

The decoupled version is easier to test,
easier to reuse,
and easier to reason about.

The screen says what happened.

The parent decides where to go.

---

## A Natural Question

We now understand that the NavController
drives navigation and manages the back stack.

But in Compose, a question appears.

```text
Where Does The NavController Live?

Who Creates It?

And How Does It Connect
To The Composable Screens?
```

That leads us to the next concept:

```text
Navigation With Compose
```

---

## Revision

### Core Idea

```text
NavController = The Driver That Follows
The Navigation Graph And Manages The Back Stack
```

### Mental Model

```text
Navigation Graph = Map

NavController = Driver

↓

navigate("search")

↓

Push Onto Back Stack

↓

Display Screen

-------------------------

popBackStack()

↓

Pop From Back Stack

↓

Display Previous Screen
```

### Production Recognition

```kotlin
val navController = rememberNavController()

navController.navigate("search")
```

↓

```text
Create Driver

↓

Follow Route To Search
```

> **You'll see this in...**
> - **DOC 4 — Channel**, where ViewModel sends one-time navigation events to the UI via Channel
> - **DOC 9 — Production Compose Patterns**, where NavController is treated as hoisted state managed at the top of the Composable tree

### Previous Concept

```text
Navigation Graph
```

### Next Concept

```text
Navigation With Compose
```
