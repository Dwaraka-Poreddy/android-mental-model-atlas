<!--
Primary Question: Who owns a ViewModel, and how long does it live?
Prerequisites: Conditional Navigation
After Reading: You understand that ViewModels belong to a scope rather than a screen, and that navigation determines the lifetime of many ViewModels in Android applications.
Next Concept: Production Navigation Patterns
-->

# ViewModel Scoping

## Looking Back

We've learned how navigation moves users between destinations, how applications organize large navigation graphs, and how navigation decisions are made based on application state.

A natural question now appears.

```text
When A Destination Opens,

Who Creates Its ViewModel?

And When Is That ViewModel Destroyed?
```

---

## The Problem

Imagine the following navigation flow.

```text
Home

↓

Product Details

↓

Reviews
```

Each destination has its own ViewModel.

```text
HomeViewModel

ProductViewModel

ReviewsViewModel
```

Now imagine the user presses the Back button.

```text
Reviews

↓

Back

↓

Product Details
```

Should `ReviewsViewModel` still exist?

Probably not.

The user has left that screen.

What about `ProductViewModel`?

Yes.

The user has returned to that screen and expects its state to still be available.

This raises an important question.

Who decides when a ViewModel is created and destroyed?

---

## A Thought Experiment

Imagine checking into a hotel.

When you arrive, you're given a room key.

The key belongs to your stay.

As long as you're staying in the hotel, the key remains valid.

When you check out, the key is no longer useful and is returned.

The hotel doesn't create a new key every few minutes, nor does it let you keep the key forever.

The key exists only for the lifetime of your stay.

ViewModels work in much the same way.

A ViewModel belongs to a **scope**.

As long as that scope exists, the ViewModel exists.

When the scope ends, the ViewModel is destroyed.

---

## ViewModel Scoping

ViewModel Scoping is the process of defining who owns a ViewModel and how long it should remain alive.

Conceptually,

```text
Scope

↓

Owns

↓

ViewModel
```

Notice that the owner is the **scope**, not necessarily the screen.

Different scopes result in different ViewModel lifetimes.

---

## Common Scopes

One common scope is a destination.

```text
Product Details

↓

ProductViewModel
```

The ViewModel remains alive while that destination exists in the navigation back stack.

Once the destination is removed from the back stack, the ViewModel is destroyed.

---

Another common scope is an entire navigation graph.

```text
Checkout Graph

↓

Shipping

↓

Payment

↓

Confirmation

↓

CheckoutViewModel
```

Every destination inside the Checkout flow shares the same ViewModel.

The ViewModel is destroyed only after the entire Checkout graph is removed.

---

Some ViewModels are scoped to an Activity.

```text
Activity

↓

Home

Search

Profile

↓

SharedViewModel
```

Every destination inside that Activity can access the same ViewModel.

The ViewModel remains alive until the Activity itself is destroyed.

---

## Why This Matters

Understanding ViewModel scopes explains many behaviors that initially seem confusing.

For example,

- why returning to a screen sometimes preserves its state,
- why two screens can intentionally share the same ViewModel,
- and why some ViewModels disappear while others continue to exist.

The answer isn't the screen itself.

The answer is the scope that owns the ViewModel.

---

## Common Misconception

A common misconception is:

```text
Every Screen Has Its Own ViewModel
```

Incorrect.

A screen often has its own ViewModel, but that isn't a rule.

Multiple destinations can intentionally share a single ViewModel if they belong to the same scope.

Likewise, a screen can receive a completely new ViewModel if its previous scope has ended.

---

## A Natural Question

We've now learned:

- why navigation exists,
- how applications organize navigation,
- how data flows between destinations,
- how large applications scale their navigation,
- and how ViewModels are scoped to navigation lifecycles.

A natural question appears.

```text
How Are All Of These Concepts Implemented In Real Android Applications?
```

The next two chapters answer that question from different perspectives.

First, we'll study the navigation architecture commonly used in large production applications.

Then we'll see how all of these concepts are implemented using Jetpack Compose.

```text
Next:

Production Navigation Patterns

↓

Navigation with Compose
```

---

## Revision

### Core Idea

```text
ViewModel Scoping = A ViewModel Lives As Long As Its Scope Lives
```

---

### Mental Model

```text
Scope

↓

ViewModel

↓

State
```

The scope owns the ViewModel.

The ViewModel owns the screen state.

---

### Production Recognition

When thinking about ViewModel scoping, remember that a ViewModel isn't tied directly to a screen.

Instead, its lifetime is determined by the scope that owns it, such as a destination, a navigation graph, or an Activity.

---

## Looking Ahead

This chapter explained **why** ViewModels have different lifetimes.

In the companion document,

**ViewModel Scoping — Production Code**,

we'll explore how Android implements different scopes using:

- `hiltViewModel()`
- Navigation back stack entries
- Graph-scoped ViewModels
- Activity-scoped ViewModels
- Shared ViewModels
- `SavedStateHandle`
- Common production patterns
- Common mistakes

---

### Previous Concept

```text
Conditional Navigation
```

---

### Next Concept

```text
Production Navigation Patterns
```