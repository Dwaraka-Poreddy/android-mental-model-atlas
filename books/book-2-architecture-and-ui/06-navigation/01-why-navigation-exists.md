# Why Navigation Exists

## Looking Back

In the previous document we learned:

```text
ViewModel

↓

Owns Screen State

-------------------------

Repository

↓

Owns Business State

-------------------------

Unidirectional Data Flow

↓

Events Flow To Owners

State Flows To Consumers
```

We now know how to build a single screen
with clean architecture.

But a natural question appears:

```text
An App Has Many Screens.

How Do They Connect?
```

---

## A Simple Observation

Consider a food delivery app.

The user opens the app and sees:

```text
Home
```

They search for a restaurant:

```text
Home → Search
```

They tap a restaurant:

```text
Home → Search → Restaurant
```

They add an item and go to cart:

```text
Home → Search → Restaurant → Cart
```

They press Back:

```text
Home → Search → Restaurant
```

Three things happened:

```text
1. The app showed different screens at different times

2. The system knew where "Back" should go

3. The previous screen's state was still there
```

None of this is accidental.

Something is managing it.

---

## The Problem

Without a system, each screen would need to know
about every other screen.

```text
Home → knows about → Search
Home → knows about → Cart
Home → knows about → Profile
Search → knows about → Restaurant
Search → knows about → Home
Restaurant → knows about → Cart
Restaurant → knows about → Search
Cart → knows about → Checkout
Cart → knows about → Restaurant
```

Every screen becomes tangled with every other screen.

Adding a new screen means updating many existing screens.

And the question remains:

```text
Who Remembers Where "Back" Goes?
```

---

## What Navigation Actually Is

Navigation is a system that manages three things:

```text
1. Which screen is currently visible

2. How screens connect to each other

3. The history of screens the user has visited
```

Think of it as a coordinator
that sits above individual screens.

```text
Navigation System

↓

Manages

↓

Which Screen Is Visible

↓

How Screens Connect

↓

Screen History
```

Individual screens don't need to know about each other.

They only need to know about the navigation system.

---

## A Mental Model

Think of the screens in an app as locations on a map.

```text
[Home] ——→ [Search] ——→ [Restaurant]
  |                          |
  ↓                          ↓
[Profile]                 [Cart] ——→ [Checkout]
```

Each location is a destination.

Each arrow is a possible transition.

The navigation system is the driver
who follows this map.

---

## The Back Stack

When the user moves through screens,
the system remembers where they came from.

```text
User taps Search:

  ┌──────────┐
  │  Search   │  ← visible
  ├──────────┤
  │   Home    │
  └──────────┘
```

User taps a restaurant:

```text
  ┌──────────┐
  │Restaurant │  ← visible
  ├──────────┤
  │  Search   │
  ├──────────┤
  │   Home    │
  └──────────┘
```

User presses Back:

```text
  ┌──────────┐
  │  Search   │  ← visible
  ├──────────┤
  │   Home    │
  └──────────┘
```

This structure is called the back stack.

```text
Back Stack = The History Of Screens The User Has Visited
```

It is a stack because the most recent screen
is always on top.

Pressing Back removes the top screen
and reveals the one beneath it.

---

## Why This Matters For Architecture

Remember from DOC 5:

```text
ViewModel

↓

Owns Screen State
```

Each screen in the back stack can have its own ViewModel.

When the user navigates to a new screen,
a new ViewModel is created for that screen.

When the user presses Back,
the screen and its ViewModel are removed from the stack.

```text
  ┌─────────────────────────┐
  │ Restaurant               │  ← visible
  │   └── RestaurantViewModel │
  ├─────────────────────────┤
  │ Search                   │
  │   └── SearchViewModel    │
  ├─────────────────────────┤
  │ Home                     │
  │   └── HomeViewModel      │
  └─────────────────────────┘
```

Navigation and architecture work together.

The back stack manages screen lifetime.

The ViewModel manages screen state.

---

## Production Notes

```text
• Navigation manages which screen is visible,
  not how the screen looks.

• The back stack is the history of screens.
  Pressing Back pops the top screen.

• Each screen in the back stack can own its own ViewModel.
  The ViewModel is scoped to that screen's lifetime.

• Without a navigation system,
  screens become tightly coupled to each other.

• Navigation is not UI.
  It is a system that coordinates screen transitions.
```

---

## Common Misconception

A common misconception is:

```text
Navigation = Moving Between Activities
```

Incorrect.

In modern Android, most apps use a single Activity
with multiple screens managed by a navigation system.

```text
Old Approach

Activity A → Activity B → Activity C

-------------------------

Modern Approach

Single Activity

↓

Navigation System

↓

Screen A → Screen B → Screen C
```

The navigation system replaces
what Activities used to do for screen transitions.

---

## A Natural Question

We now understand that navigation is a system
that manages screens, transitions, and history.

But a question immediately appears.

```text
How Does The System Know
Which Screens Exist
And How They Connect?
```

The system needs a map.

That leads us to the next concept:

```text
Navigation Graph
```

---

## Revision

### Core Idea

```text
Navigation = A System That Manages
Which Screen Is Visible,
How Screens Connect,
And The History Of Visited Screens
```

### Mental Model

```text
[Home] ——→ [Search] ——→ [Restaurant]
  |                          |
  ↓                          ↓
[Profile]                 [Cart]

        ↓

    Back Stack

        ↓

  ┌──────────┐
  │ Current   │  ← visible
  ├──────────┤
  │ Previous  │
  ├──────────┤
  │ Earlier   │
  └──────────┘
```

### Production Recognition

```text
Single Activity App

↓

Navigation System

↓

Multiple Screens + Back Stack
```

> **You'll see this in...**
> - **DOC 7 — Jetpack Compose**, where each screen is a Composable function that the navigation system displays
> - **DOC 9 — Production Compose Patterns**, where navigation events are hoisted out of Composables into ViewModels
> - **DOC 11 — Remote Communication**, where navigating to a screen may trigger data loading via its ViewModel

### Previous Concept

```text
DOC 5 — Unidirectional Data Flow
```

### Next Concept

```text
Navigation Graph
```
