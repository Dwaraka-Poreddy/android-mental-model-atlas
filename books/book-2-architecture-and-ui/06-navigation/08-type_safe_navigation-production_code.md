# Type-Safe Navigation — Production Code

## Purpose

Understand how Android applications implement type-safe navigation, recognize the different approaches used across Android projects, and know when each approach is appropriate.

---

## Looking Back

In the previous chapter, we learned that navigation becomes safer when destinations receive the correct arguments before the application runs.

Android has introduced multiple solutions over the years to achieve this goal.

As a production Android developer, you'll likely encounter several of them depending on the age of the project and whether it uses Fragments or Jetpack Compose.

---

# Evolution of Type-Safe Navigation

Android's navigation ecosystem has evolved significantly.

```text
Manual Route Strings

↓

Safe Args (Fragments)

↓

Typed Routes (Compose Navigation)

↓

Kotlin Serialization & Generated Routes
```

Each generation reduces the chances of runtime navigation errors.

---

# Recognition

When exploring a production codebase, you'll commonly encounter:

```kotlin
navController.navigate("product/42")
```

```kotlin
ProductDetailsDirections.actionHomeToProduct(productId)
```

```kotlin
@Serializable
data class ProductRoute(val productId: Int)
```

```kotlin
navController.navigate(ProductRoute(42))
```

Each represents a different approach to passing navigation arguments safely.

---

# Pattern 1 — Manual Route Strings

The simplest approach builds routes manually.

```kotlin
navController.navigate("product/$productId")
```

Although simple,

this approach has several drawbacks:

- Typographical mistakes
- Wrong argument names
- Missing arguments
- Incorrect data types

These problems are only discovered at runtime.

---

# Pattern 2 — Safe Args (Fragment Navigation)

Fragment-based applications often use the Safe Args Gradle plugin.

```kotlin
val action =
    HomeFragmentDirections
        .actionHomeToProduct(productId)

findNavController().navigate(action)
```

Instead of manually constructing routes,

generated classes ensure:

- required arguments are provided,
- argument names are correct,
- argument types match the destination.

Compile-time validation catches mistakes much earlier.

---

# Pattern 3 — Typed Routes (Compose Navigation)

Modern Compose Navigation increasingly favors strongly typed routes.

```kotlin
@Serializable
data class ProductRoute(
    val productId: Int
)

navController.navigate(
    ProductRoute(productId)
)
```

Instead of building route strings,

developers navigate using strongly typed objects.

This approach is:

- easier to refactor,
- easier to read,
- significantly safer.

---

# Pattern 4 — Parcelable & Serializable

Sometimes destinations require more than a single identifier.

Android supports passing structured objects.

```kotlin
@Parcelize
data class Product(...)
```

or

```kotlin
@Serializable
data class Product(...)
```

However,

production applications usually prefer passing lightweight identifiers instead of entire business objects.

For example,

prefer:

```text
Product ID
```

instead of:

```text
Complete Product Object
```

The destination can then load the remaining data from a repository.

---

# Pattern 5 — Reading Navigation Arguments

After navigation completes,

the destination needs a way to access the arguments that were passed to it.

In Android, this is commonly done using `SavedStateHandle`.

```kotlin
@HiltViewModel
class ProductViewModel(
    private val savedStateHandle: SavedStateHandle
) : ViewModel() {

    private val productId: Int =
        checkNotNull(savedStateHandle["productId"])
}
```

Notice that `SavedStateHandle` is **not responsible for making navigation type-safe**.

Its responsibility is to expose the navigation arguments to the destination after navigation has completed.

If the project uses type-safe navigation (such as Safe Args or typed routes), the values stored inside `SavedStateHandle` are already validated before the ViewModel receives them.

> **Already covered:** **book-1-foundations → 05-android-lifecycle-architecture → 12-saved-state-handle.md**, where we learned that `SavedStateHandle` stores the minimum information required to reconstruct screen state after process recreation. Here, we're focusing only on its additional role as a convenient way to access navigation arguments inside a ViewModel.

---

# Choosing The Right Approach

| Project Type | Recommended Approach |
|--------------|----------------------|
| Legacy Fragment apps | Safe Args |
| New Compose apps | Typed Routes |
| Small prototypes | Manual routes (acceptable) |
| Large production apps | Typed routes with serialization |

---

# How To Read Production Code

When you see:

```kotlin
HomeFragmentDirections...
```

read it as:

```text
Generated code that guarantees
correct navigation arguments.
```

---

When you see:

```kotlin
navController.navigate(ProductRoute(...))
```

read it as:

```text
Navigation using
strongly typed route objects.
```

---

When you see:

```kotlin
SavedStateHandle
```

read it as:

```text
Retrieve navigation arguments
inside the ViewModel.
```

---

# Common Production Mistakes

## Building Route Strings Everywhere

```kotlin
"product/$id"
"profile/$userId"
"cart/$cartId"
```

These strings become difficult to maintain as applications grow.

Prefer generated or strongly typed navigation whenever possible.

---

## Passing Large Business Objects

Avoid navigating with entire domain models.

Instead of:

```text
Product
```

prefer:

```text
Product ID
```

The destination should retrieve fresh data from the repository.

---

## Making Every Argument Nullable

Required arguments should remain required.

Making everything nullable often hides programming mistakes until runtime.

---

## Bypassing Generated Navigation

Avoid manually constructing route strings when the project already uses Safe Args or typed routes.

Doing so removes the compile-time guarantees those systems provide.

---

# Key Takeaways

```text
✓ Android has evolved from manual routes to strongly typed navigation.

✓ Safe Args remains common in Fragment-based applications.

✓ Typed routes are becoming the preferred approach in modern Compose applications.

✓ Prefer lightweight identifiers over large business objects.

✓ Retrieve navigation arguments through SavedStateHandle when appropriate.

✓ Compile-time validation prevents many navigation bugs before the application runs.
```