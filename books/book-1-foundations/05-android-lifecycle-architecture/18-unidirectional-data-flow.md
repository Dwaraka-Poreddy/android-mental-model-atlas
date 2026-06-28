# Unidirectional Data Flow

## Observation

So far we have learned:

```text
Repository

↓

Owns Business State

↓

ViewModel

↓

Owns UI State

↓

UI Renders UI State
```

A natural question appears:

```text
How Should Data Move Through The Application?
```

---

## The Problem

Suppose the user taps:

```text
Add To Cart
```

Should the UI directly update the cart?

```text
UI

↓

Cart.total = ₹900
```

Should the ViewModel update it?

```text
ViewModel

↓

cart.total = ₹900
```

Should the Repository update it?

```text
Repository

↓

cart.total = ₹900
```

If every layer can modify every state:

```text
        UI
      ↗  ↓  ↘
ViewModel ↔ Repository
      ↖  ↓  ↙
```

it becomes difficult to answer:

```text
Who Changed The State?
```

---

## Unidirectional Data Flow

The solution is to make every state change follow one predictable path.

```text
User Event

↓

ViewModel

↓

Repository

↓

Business State Updated

↓

ViewModel

↓

UI State Updated

↓

UI Re-renders
```

Every update follows the same flow.

---

## Mental Model

Think of the application as a loop.

```text
User Event

↓

ViewModel

↓

Repository

↓

Business State

↓

ViewModel

↓

UI State

↓

UI

↓

User Event
```

Events travel toward the owners.

State travels toward the consumers.

---

## A Simple Example

The user presses:

```text
Add To Cart
```

Step 1:

```text
UI

↓

Reports User Event
```

Step 2:

```text
ViewModel

↓

Coordinates The Action
```

Step 3:

```text
Repository

↓

Updates Cart
```

Step 4:

```text
Business State Changes

↓

ViewModel Produces New UI State

↓

UI Automatically Re-renders
```

The UI never manually refreshes itself.

It simply renders the latest UI State.

---

## Minimal Code

```kotlin
Button(
    onClick = {
        viewModel.addToCart(product)
    }
)
```

```kotlin
fun addToCart(product: Product) {
    repository.add(product)
}
```

---

## Production Code

```kotlin
class CartViewModel(
    private val repository: CartRepository
) : ViewModel() {

    val uiState =
        repository.cart
            .map { cart ->

                CartUiState(
                    items = cart.items,
                    total = cart.totalPrice
                )

            }

}
```

The ViewModel observes Business State,

transforms it into UI State,

and exposes it to the UI.

---

## How To Read It

Don't read:

```text
Button

↓

ViewModel

↓

Repository
```

Read:

```text
User Event

↓

Business State Updated

↓

New UI State Produced

↓

UI Re-renders
```

Focus on the direction of information flow rather than individual API calls.

---

## Production Notes

```text
• User interactions are represented as events.

• Business State is owned by the Repository.

• ViewModels transform Business State into UI State.

• The UI renders UI State instead of modifying state directly.

• Following one predictable flow makes applications easier to understand and debug.
```

---

## Putting Everything Together

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

↓

User Event

↓

ViewModel
```

The same ownership principles from previous chapters naturally produce a predictable data flow.

---

## Revision

### Core Idea

```text
Events

↓

Flow To Owners

-------------------------

State

↓

Flows To Consumers
```

### Mental Model

```text
User Event

↓

ViewModel

↓

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
viewModel.addToCart(product)
```

↓

```text
User Event

↓

ViewModel

↓

Repository

↓

Business State Update

↓

New UI State

↓

UI Re-render
```

### Previous Concept

```text
Business State
```

### End Of Document

```text
Application Data

↓

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

The concepts in this document form a single ownership and data-flow model that appears throughout modern Android applications.
