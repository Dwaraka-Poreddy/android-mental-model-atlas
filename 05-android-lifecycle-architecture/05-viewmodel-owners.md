# ViewModel Owners

## Looking Back

In the previous chapters we learned:

```text
ViewModel

↓

Owns Screen State
```

```text
ViewModelStore

↓

Preserves ViewModels
```

```text
viewModelScope

↓

Owns Screen Coroutines
```

and

```text
SavedStateHandle

↓

Owns Reconstruction Information
```

A natural question appears:

```text
Can Multiple Screens Share The Same ViewModel?
```

More importantly:

```text
Who Should Own This ViewModel?
```

---

## A Simple Observation

Imagine a checkout flow.

```text
Checkout

↓

Cart

↓

Address

↓

Payment
```

Every screen needs:

```text
Selected Items Coupon Total Price
```

Should every screen create its own ViewModel?

```text
CartViewModel

AddressViewModel

PaymentViewModel
```

Each would now contain:

```text
Selected Items

Coupon

Total Price
```

The same state would exist in multiple places.

---

## A Better Design

Instead:

```text
Checkout Activity

↓

CheckoutViewModel

↓

Cart Fragment

↓

Address Fragment

↓

Payment Fragment
```

Now:

```text
One ViewModel

↓

One Screen State

↓

Multiple Screens
```

Every screen observes the same source of truth.

---

## The Core Idea

A ViewModel always belongs to an owner.

The owner determines:

```text
Lifetime

↓

Sharing

↓

Destruction
```

Instead of asking:

```text
How Many Screens Use This ViewModel?
```

ask:

```text
Who Owns This ViewModel?
```

---

## Example 1 — Fragment Owner

```text
Product Fragment

↓

ProductViewModel
```

Only that Fragment uses the ViewModel.

When the Fragment is removed:

```text
Fragment Removed

↓

ProductViewModel Removed
```

---

## Example 2 — Activity Owner

```text
Checkout Activity

↓

CheckoutViewModel

↓

Cart Fragment

↓

Address Fragment

↓

Payment Fragment
```

All Fragments share the same ViewModel.

When Checkout finishes:

```text
Checkout Activity Removed

↓

CheckoutViewModel Removed
```

---

## Example 3 — Navigation Owner

```text
Product Flow

↓

Product

↓

Reviews

↓

Specifications
```

All screens share:

```text
ProductViewModel
```

The owner is:

```text
Navigation Graph
```

When the navigation flow ends:

```text
Navigation Graph Removed

↓

ProductViewModel Removed
```

---

## Minimal Code

```kotlin
private val vm by activityViewModels<CheckoutViewModel>()
```

Focus on the ownership,

not the API.

---

## Production Code

```kotlin
class CartFragment : Fragment() {

    private val viewModel by activityViewModels<CheckoutViewModel>()

}
```

### How To Read It

Don't read:

```text
activityViewModels()
```

Read:

```text
Checkout Activity

↓

Owns CheckoutViewModel

↓

Cart Fragment Requests It
```

The Fragment is using a ViewModel owned by its Activity.

---

## Production Notes

```text
• A ViewModel can be shared by multiple screens.

• The owner determines the ViewModel lifetime.

• Sharing state is often better than duplicating state.

• Modern Android applications commonly scope ViewModels to Fragments, Activities or Navigation graphs.
```

---

## Another Common Misconception

A common misconception is:

```text
One Screen

↓

One ViewModel
```

Incorrect.

The better question is:

```text
Who Owns This ViewModel?
```

Multiple screens can safely share a ViewModel if they share the same owner.

---

## Platform Perspective

The API is Android-specific.

The architectural idea is universal.

For example:

```text
Flutter

↓

Provider

↓

Multiple Widgets Share State
```

or

```text
React

↓

Context

↓

Multiple Components Share State
```

Different platforms provide different APIs,

but the underlying idea is the same:

```text
One Owner

↓

One Shared State

↓

Multiple Consumers
```

---

## Putting Everything Together

```text
Owner

↓

ViewModelStore

↓

ViewModel

↓

viewModelScope

↓

StateFlow

↓

Screen State

↓

Activity / Fragment Displays State
```

Ownership determines lifetime,

sharing,

and destruction.

---

## Reading Production Code

```kotlin
private val vm by activityViewModels<CheckoutViewModel>()
```

Read it as:

```text
Checkout Activity

↓

Owns CheckoutViewModel

↓

Cart Fragment Requests Shared State
```

instead of:

```text
activityViewModels()

↓

Android API
```

Focus on responsibility rather than syntax.

---

## Revision

### Core Idea

```text
A ViewModel Always Has An Owner
```

### Mental Model

```text
Owner

↓

ViewModel

↓

Screen State
```

### Key Principle

```text
Owner

↓

Determines

Lifetime

↓

Determines

Sharing

↓

Determines

Destruction
```

### Production Recognition

```kotlin
viewModels()

activityViewModels()

navGraphViewModels()
```

↓

```text
Different Owners

Different Lifetimes

Different Sharing
```

### Previous Concept

```text
SavedStateHandle
```

### Next Concept

```text
Repository
```
