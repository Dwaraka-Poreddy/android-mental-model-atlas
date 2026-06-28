# Business State

## Observation

Consider an e-commerce application.

```text
Home

↓

Cart

↓

Checkout

↓

Orders
```

Suppose the user adds an item to the cart.

Immediately:

```text
Home

↓

Cart Badge = 2

-------------------------

Cart

↓

2 Items

-------------------------

Checkout

↓

Total = ₹900
```

Multiple screens reflect the same information.

A natural question appears:

```text
Who Owns This State?
```

---

## A Simple Example

Close every screen:

```text
Home Closed

Cart Closed

Checkout Closed
```

Does the cart still exist?

```text
Yes.
```

The cart is not a property of any particular screen.

It is a property of the application itself.

---

## Business State

Business State represents:

```text
State That Belongs To The Application Not To A Particular Screen
```

Typical examples include:

```text
Current User

Shopping Cart

Authentication Status

Selected Language

Theme
```

These values may be used by multiple screens simultaneously.

---

## Mental Model

```text
Repository

↓

Business State

↓

ViewModel

↓

UI State

↓

UI
```

The Repository owns Business State.

The ViewModel transforms Business State into UI State.

The UI renders UI State.

---

## UI State vs Business State

```text
UI State

↓

Current Screen

↓

Loading Spinner

↓

Selected Tab

↓

Dialog Visibility
```

```text
Business State

↓

Application

↓

Current User

↓

Cart

↓

Authentication

↓

Theme
```

UI State belongs to a screen.

Business State belongs to the application.

---

## Minimal Code

```kotlin
class CartRepository {

    val cart: StateFlow<Cart>

}
```

Every screen that needs the cart observes the same Business State.

---

## Production Code

```kotlin
class CartViewModel(
    private val cartRepository: CartRepository
) : ViewModel() {

    val uiState = cartRepository.cart
        .map { cart ->

            CartUiState(
                items = cart.items,
                total = cart.totalPrice
            )

        }

}
```

The ViewModel does not own the cart.

It owns a UI representation of the cart.

---

## How To Read It

Don't read:

```text
CartRepository

↓

cart
```

Read:

```text
Repository

↓

Owns Shared Application State

↓

ViewModel

↓

Transforms It

↓

UI
```

---

## Production Notes

```text
• Business State belongs to the application, not a particular screen.

• Multiple ViewModels may observe the same Business State.

• Repositories are common owners of Business State.

• UI State is typically derived from Business State.
```

---

## Revision

### Core Idea

```text
Business State = State That Belongs To The Application Not To A Particular Screen
```

### Mental Model

```text
Repository

↓

Business State

↓

ViewModel

↓

UI State

↓

UI
```

### Production Recognition

```kotlin
private val cartRepository: CartRepository
```

↓

```text
This ViewModel Reads Shared Application State Instead Of Owning It
```

### Previous Concept

```text
UI State
```

### Next Concept

```text
Unidirectional Data Flow
```
