# MutableStateFlow

## Looking Back

In the previous chapter we learned that:

```text
StateFlow

↓

Represents

The Current State + Future State Updates
```

Multiple parts of an application can observe the same StateFlow and always know the current state.

A natural question appears:

```text
If The State Changes, Who Changes It?
```

---

## A Simple Observation

Consider a shopping cart.

Its current state is:

```text
Items: 3

Total: ₹1250
```

Many different screens may need to display this information.

They all want to:

```text
Read The Current Cart State
```

But should every screen also be allowed to change:

```text
Items: 3

↓

Items: 100
```

Probably not.

Applications usually separate:

```text
Reading From Updating
```

---

## Another Mental Model

Imagine a Google document.

The document contains:

```text
Sprint Plan Monday 10:00 AM
```

Two people open the same document.

The first person has:

```text
View Access
```

They can:

```text
Read

Refresh

See New Changes
```

But they cannot:

```text
Edit

Delete

Modify
```

---

The second person has:

```text
Edit Access
```

They can:

```text
Read

Refresh

See New Changes + Modify The Document
```

Notice something important.

There are not two documents.

There is still:

```text
One Document
```

The only difference is:

```text
Permission
```

---

## StateFlow And MutableStateFlow

StateFlow and MutableStateFlow work in the same way.

They both represent:

```text
The Same Current State
```

The difference is simply:

```text
StateFlow

↓

Read Only

-------------------------

MutableStateFlow

↓

Read

+

Update
```

MutableStateFlow is not a different kind of state.

It represents exactly the same state.

It simply allows the owner to modify it.

---

## Android Example

Suppose we have:

```kotlin
data class CartState(

    val itemCount: Int,

    val totalPrice: Double
)
```

Inside a ViewModel we create:

```kotlin
private val _cartState =
    MutableStateFlow(
        CartState(
            itemCount = 0,
            totalPrice = 0.0
        )
    )
```

The ViewModel owns the state and is responsible for updating it.

---

## Exposing Read-Only State

Instead of exposing the MutableStateFlow directly, applications usually expose:

```kotlin
val cartState: StateFlow<CartState> =
    _cartState
```

Now other parts of the application can:

```text
Observe The Current Cart State
```

without being able to modify it.

---

## Updating State

Suppose the user adds a new item.

The ViewModel updates the state:

```kotlin
_cartState.value =
    _cartState.value.copy(
        itemCount = 1,
        totalPrice = 499.0
    )
```

Every observer immediately sees:

```text
Items: 1

Total: ₹499
```

without directly changing the state themselves.

---

## Another Android Example

A login screen may use:

```kotlin
private val _loginState =
    MutableStateFlow(
        LoginState(
            username = "",
            isLoading = false,
            isLoggedIn = false
        )
    )

val loginState: StateFlow<LoginState> =
    _loginState
```

The ViewModel updates:

```text
Loading

false

↓

true

↓

false
```

The UI simply observes those changes.

---

## Why Expose StateFlow Instead?

Imagine every screen received a MutableStateFlow.

Any screen could execute:

```kotlin
_cartState.value =
    CartState(
        itemCount = 999,
        totalPrice = 0.0
    )
```

Now multiple parts of the application are independently changing the same state.

As applications grow, this quickly becomes difficult to reason about.

Restricting most consumers to:

```text
Read Only
```

creates a clear ownership model.

---

## Another Common Misconception

A common misconception is:

```text
StateFlow And MutableStateFlow Represent Different States
```

Incorrect.

They both represent:

```text
Exactly The Same Current State
```

The only difference is:

```text
Can It Be Updated?
```

---

## Another Common Misconception

A common misconception is:

```text
Every Consumer Needs MutableStateFlow
```

Usually only the owner of the state needs update capability.

Everyone else simply needs to:

```text
Observe The Current State
```

---

## Putting Everything Together

```text
                    Shopping Cart State

               Items: 3
               Total: ₹1250

                      ▲            ▲

                      │            │

              StateFlow      MutableStateFlow

              Read Only      Read + Update
```

There is still:

```text
One State
```

The difference is simply:

```text
Permission
```

---

## The Key Mental Model

Think of a Google document.

```text
One Document

↓

Viewer

↓

Read Only

-------------------------

One Document

↓

Editor

↓

Read

+

Update
```

Similarly:

```text
One State

↓

StateFlow

↓

Read Only

-------------------------

One State

↓

MutableStateFlow

↓

Read

+

Update
```

---

## Revision Table

| Section | Content |
|----------|----------|
| Core Idea | MutableStateFlow represents the same state with update capability |
| Mental Model | Google Docs view vs edit access |
| StateFlow | Read only |
| MutableStateFlow | Read + update |
| Android Examples | Shopping cart, Login screen |
| Common Misconception | They do not represent different states |
| Previous Concept | StateFlow |
| Next Concept | StateFlow vs Flow |
