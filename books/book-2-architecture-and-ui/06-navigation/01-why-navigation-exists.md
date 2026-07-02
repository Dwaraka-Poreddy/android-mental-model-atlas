<!--
Primary Question: Why does Android need a navigation system?
Prerequisites: DOC 5 — Unidirectional Data Flow
After Reading: You understand why applications need a dedicated system to coordinate screens instead of letting screens communicate directly.
Next Concept: Navigation Graph
-->

# Why Navigation Exists

## Looking Back

In the previous document, we learned:

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

We now know how to build a single screen with clean architecture.

A natural question appears.

```text
An App Has Many Screens.

How Do They Work Together?
```

---

## The Problem

Imagine building a food delivery app.

The user opens the app.

```text
Home
```

They search for a restaurant.

```text
Home → Search
```

They open a restaurant.

```text
Home → Search → Restaurant
```

They add food to the cart.

```text
Home → Search → Restaurant → Cart
```

Now imagine every screen directly controlled every other screen.

```text
Home

├── Search

├── Cart

├── Profile

└── Orders

-------------------------

Search

├── Home

├── Restaurant

└── Cart

-------------------------

Restaurant

├── Search

├── Cart

└── Checkout
```

Every new screen creates more connections.

Every change affects multiple existing screens.

The application quickly becomes difficult to understand and maintain.

There must be a better way.

---

## Navigation

Instead of letting screens communicate directly,

Android applications introduce a dedicated navigation system.

Conceptually,

the architecture changes from this:

```text
Screen

↓

Another Screen

↓

Another Screen
```

to this:

```text
Screen

↓

Navigation System

↓

Another Screen
```

Screens no longer decide where to go next.

They ask the navigation system.

The navigation system decides what happens.

---

## A Mental Model

Imagine a city.

Buildings don't decide how people travel between them.

Roads,

traffic signals,

and maps coordinate movement.

```text
Home

↓

Road Network

↓

Restaurant

↓

Road Network

↓

Cart
```

The buildings simply exist.

The transportation system connects them.

Navigation works in exactly the same way.

Screens focus on displaying information.

The navigation system coordinates movement between them.

---

## What Navigation Manages

Conceptually,

the navigation system has three responsibilities.

```text
Navigation System

↓

Knows Which Screens Exist

↓

Coordinates Movement Between Screens

↓

Remembers The User's Journey
```

Individual screens remain focused on their own responsibilities.

The navigation system coordinates how those screens work together.

---

## Why This Scales Better

When a new screen is added,

existing screens don't need to learn about every possible destination.

Instead,

they simply request navigation.

```text
Restaurant Screen

↓

Navigate To Checkout

↓

Navigation System

↓

Checkout Screen
```

Each screen remains independent.

The navigation system becomes the single coordinator for moving through the application.

---

## Common Misconception

A common misconception is:

```text
Navigation = Moving Between Activities
```

Incorrect.

Modern Android applications commonly use:

```text
Single Activity

↓

Navigation System

↓

Multiple Screens
```

The navigation system coordinates screen transitions inside the application.

Activities are no longer the primary navigation mechanism.

---

## A Natural Question

We've learned why applications need a navigation system.

A natural question appears.

```text
If A Navigation System Coordinates Screens, How Does It Know Which Screens Exist And How They're Connected?
```

It needs a map.

That leads us to the next concept.

```text
Navigation Graph
```

---

## Revision

### Core Idea

```text
Navigation = The System That Coordinate Movement Between Screens
```

### Mental Model

```text
Screen

↓

Navigation System

↓

Another Screen
```

Screens don't navigate directly.

They ask the navigation system.

---

### Production Recognition

When reading a production Android application,

look for a single navigation system that coordinates screen transitions.

Individual screens request navigation.

They don't know how the transition is performed.

---

### Previous Concept

```text
Unidirectional Data Flow
```

### Next Concept

```text
Navigation Graph
```
