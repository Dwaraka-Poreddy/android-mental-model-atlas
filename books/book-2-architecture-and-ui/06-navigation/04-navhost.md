<!--
Primary Question: Where is the current destination actually displayed?
Prerequisites: NavController
After Reading: You understand that the NavHost is a container whose responsibility is to display the current destination selected by the NavController.
Next Concept: Destinations, Navigation & Arguments
-->

# NavHost

## Looking Back

In the previous chapter,

we learned that the NavController is responsible for performing navigation.

Conceptually,

it acts as the driver of the navigation system.

```text
Navigation Graph

↓

NavController

↓

Current Destination
```

A natural question appears.

```text
The NavController Knows Which Destination Should Be Visible. But Where Is That Destination Actually Displayed?
```

---

## The Problem

Imagine the user opens an application.

The current destination is:

```text
Home
```

The user taps Search.

The NavController updates the current destination.

```text
Search
```

The user opens a product.

```text
Product Details
```

The NavController always knows

which destination is currently active.

But simply knowing the current destination

doesn't make it appear on the screen.

Something still needs to display it.

---

## NavHost

The **NavHost** is the container that displays the current destination.

Conceptually,

it sits between the navigation system

and the user.

```text
Navigation Graph

↓

NavController

↓

NavHost

↓

Visible Screen
```

Whenever the current destination changes,

the NavHost updates what it is displaying.

---

## A Mental Model

Imagine a digital photo frame.

The frame itself never changes.

Only the photo inside it changes.

```text
Photo Frame

↓

Family Photo

↓

Vacation Photo

↓

Birthday Photo
```

The frame remains the same.

Only its contents are replaced.

The NavHost works in a very similar way.

```text
NavHost

↓

Home

↓

Search

↓

Product Details

↓

Cart
```

The NavHost always remains in the application.

Only the destination it displays changes.

---

## One Destination At A Time

One question naturally comes to mind.

```text
Does The NavHost Contain Every Screen?
```

No.

Conceptually,

the NavHost only displays the current destination.

```text
NavHost

↓

Home
```

Navigate to Search.

```text
NavHost

↓

Search
```

Navigate to Product Details.

```text
NavHost

↓

Product Details
```

Notice what happened.

The displayed destination changed.

The NavHost itself did not.

---

## What Happened To The Previous Screen?

If the NavHost only displays one destination,

another question naturally appears.

```text
Where Did Home Go?
```

The previous destination is no longer being displayed.

However,

the navigation system still remembers it.

Exactly how that history is remembered is the responsibility of another concept.

We'll explore that when we study the **Back Stack**.

For now, it's enough to remember this distinction.

```text
NavHost

↓

Displays The Current Destination

-------------------------

Navigation System

↓

Remembers Previous Destinations
```

---

## Responsibilities

By now, the navigation system consists of three distinct parts.

```text
Navigation Graph

↓

Describes Navigation

-------------------------

NavController

↓

Performs Navigation

-------------------------

NavHost

↓

Displays The Current Destination
```

Each component has one responsibility. Together, they make navigation possible.

---

## Why This Matters

Separating these responsibilities keeps the navigation system simple.

The Navigation Graph describes what is possible.

The NavController decides where to go.

The NavHost displays what the user currently sees.

Each component focuses on one job, making the system easier to understand and maintain.

---

## Common Misconception

A common misconception is:

```text
The NavHost Stores Every Screen
```

Incorrect.

The NavHost only displays the current destination.

The navigation system is responsible for remembering previously visited destinations.

Think of it this way.

```text
NavHost

↓

Current Destination
```

Not

```text
NavHost

↓

Home

Search

Product Details

Cart
```

Only one destination is displayed at any moment.

---

## A Natural Question

We now understand:

- how navigation is described,
- who performs navigation,
- and where the current destination is displayed.

A natural question appears.

```text
How Does The Application Actually Move From One Destination To Another? And How Does It Pass Data During Navigation?
```

That leads us to the next concept.

```text
Navigate & Arguments
```

---

## Revision

### Core Idea

```text
NavHost = The Container That Displays The Current Destination
```

---

### Mental Model

```text
Navigation Graph

↓

NavController

↓

NavHost

↓

Visible Destination
```

The Navigation Graph describes navigation.

The NavController performs navigation.

The NavHost displays the current destination.

---

### Production Recognition

When exploring a production Android application,

look for a single container responsible for displaying whichever destination is currently active.

The destination changes over time.

The container remains the same.

---

### Previous Concept

```text
NavController
```

---

### Next Concept

```text
Navigate & Arguments
```