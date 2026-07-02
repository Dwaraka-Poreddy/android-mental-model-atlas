# NavController — Production Code

## Purpose

Understand how production Android applications use the NavController and learn to recognize the navigation patterns commonly found in real codebases.

---

## Looking Back

In the previous chapter, we learned that the NavController is responsible for performing navigation and managing the navigation back stack.

This chapter focuses on how production Android applications use the NavController while keeping navigation clean, testable, and maintainable.

---

# Recognition

When exploring a production Android project, you'll commonly encounter navigation requests such as:

```kotlin
navController.navigate(...)
```

```kotlin
navController.popBackStack()
```

```kotlin
navController.navigateUp()
```

or callbacks like:

```kotlin
onProfileClick = { ... }

onCheckoutClick = { ... }

onBackClick = { ... }
```

These patterns indicate that the application is delegating navigation through a central NavController rather than allowing individual screens to control navigation directly.

---

# Pattern 1 — A Single NavController

Most Compose applications create one NavController for the application's primary navigation graph.

```kotlin
val navController = rememberNavController()

NavHost(
    navController = navController,
    ...
)
```

Every navigation request flows through this controller.

The NavController becomes the single coordinator for navigation within that graph.

---

# Pattern 2 — Hoisting Navigation

Production applications usually create the NavController near the top of the UI hierarchy.

```kotlin
@Composable
fun App() {

    val navController = rememberNavController()

    AppNavigation(
        navController = navController
    )

}
```

Individual screens don't create their own NavController.

Instead, the application owns it and passes navigation actions downward.

This ensures the navigation state remains consistent across the application.

---

# Pattern 3 — Screens Receive Callbacks

Screens rarely call `navController.navigate(...)` directly.

Instead, they expose callbacks.

```kotlin
HomeScreen(

    onProfileClick = {
        navController.navigate("profile")
    },

    onCartClick = {
        navController.navigate("cart")
    }

)
```

The screen reports what happened.

The parent decides where navigation should go.

This keeps UI components independent of the navigation framework.

---

# Pattern 4 — ViewModel Requests Navigation

Business logic often determines when navigation should happen.

For example,

```text
Place Order

↓

Repository

↓

Success

↓

Navigate To Confirmation
```

The ViewModel decides that navigation should occur.

The UI performs the actual navigation.

This preserves Unidirectional Data Flow and keeps ViewModels independent of Android navigation APIs.

---

# Pattern 5 — Different Navigation Operations

Production applications commonly use several navigation operations.

```kotlin
navController.navigate(...)
```

Move to another destination.

---

```kotlin
navController.popBackStack()
```

Return to the previous destination.

---

```kotlin
navController.navigateUp()
```

Navigate upward according to the application's hierarchy.

Each operation serves a different purpose even though all are performed by the NavController.

---

# Pattern 6 — One NavController Per Graph

Applications typically use one NavController for each independent navigation graph.

For example,

```text
Main Graph

↓

Main NavController
```

A separate navigation graph may have its own controller when it manages an independent flow.

Understanding which graph owns a NavController helps explain how navigation is coordinated throughout the application.

---

# How To Read Production Code

When you see:

```kotlin
rememberNavController()
```

read it as:

```text
Create

The Navigation Coordinator
```

---

When you see:

```kotlin
navController.navigate(...)
```

read it as:

```text
Request Navigation

To Another Destination
```

---

When you see:

```kotlin
onProfileClick = { ... }
```

read it as:

```text
The Screen Reports

A User Action

The Parent Performs Navigation
```

---

When you see navigation triggered after a ViewModel operation,

read it as:

```text
Business Logic

↓

Navigation Request

↓

UI Executes Navigation
```

---

# Common Production Mistakes

## Passing NavController Everywhere

Avoid giving every Composable direct access to the NavController.

Instead, expose navigation through callbacks.

This keeps UI components reusable and easier to test.

---

## Navigating From Business Logic

ViewModels should decide *that* navigation should happen.

The UI should decide *how* it happens using the NavController.

Keeping these responsibilities separate preserves a clean architecture.

---

## Creating Multiple NavControllers Unnecessarily

A navigation graph generally owns one NavController.

Creating additional controllers without a clear architectural reason makes navigation harder to understand.

---

## Mixing Navigation With UI Logic

Avoid scattering navigation calls throughout deeply nested Composables.

Keep navigation close to the part of the UI that owns the navigation graph.

---

# Key Takeaways

```text
✓ The NavController is the central coordinator for navigation.

✓ Production applications usually create one NavController per navigation graph.

✓ Screens communicate user actions through callbacks rather than navigating directly.

✓ ViewModels decide when navigation should occur; the UI performs it.

✓ The NavController manages navigation state for the graph that owns it.

✓ Keeping navigation centralized makes applications easier to test and maintain.
```