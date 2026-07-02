<!--
Primary Question: How does an application move between destinations and pass information during navigation?
Prerequisites: NavHost
After Reading: You understand that navigation changes the current destination, and arguments carry the information needed by the destination after navigation.
Next Concept: Back Stack
-->

# Navigate & Arguments

## Looking Back

In the previous chapters, we built the navigation system piece by piece.

```text
Navigation Graph

↓

Defines Navigation

-------------------------

NavController

↓

Performs Navigation

-------------------------

NavHost

↓

Displays The Current Destination
```

A natural question appears.

```text
How Does The Application Actually Move From One Destination To Another?
```

---

## The Problem

Imagine an e-commerce application.

The user opens the app.

```text
Home
```

They tap a product.

The application should open:

```text
Product Details
```

But another question immediately appears.

```text
Which Product?
```

The application needs to do two things.

```text
1. Move To Another Destination

2. Tell That Destination Which Product To Display
```

Navigation isn't only about moving.

Sometimes, it also carries information.

---

## A Thought Experiment

Imagine booking a taxi.

Simply saying

```text
Take Me Somewhere
```

isn't enough.

You need to tell the driver where to go.

```text
Take Me

↓

Airport
```

Sometimes,

you also provide additional information.

```text
Take Me

↓

Airport

↓

Terminal 2
```

The destination tells the driver where to go.

The additional information provides the details needed after arriving.

Navigation in Android works in a very similar way.

Every navigation request contains:

- a destination,
- and sometimes additional information.

---

## Navigation

Conceptually,

navigation changes the current destination.

```text
Home

↓

Navigate

↓

Search
```

Or

```text
Search

↓

Navigate

↓

Product Details
```

The Navigation Graph defines whether the move is possible.

The NavController performs the navigation.

The NavHost displays the new destination.

---

## Destination

Every navigation request has a destination.

A destination is simply the place the user wants to reach.

For example,

```text
Home

Search

Product Details

Cart

Checkout
```

The destination answers one question.

```text
Where Should The User Go?
```

---

## Arguments

Sometimes, the destination alone isn't enough.

Imagine opening:

```text
Product Details
```

Which product?

The destination needs additional information.

Conceptually,

```text
Destination + Arguments

↓

Navigation Request
```

For example,

```text
Product Details

↓

Product ID = 42
```

or

```text
User Profile

↓

User ID = 123
```

Arguments answer another question.

```text
What Does This Destination Need To Display?
```

---

## Why This Matters

Separating the destination from its arguments makes navigation flexible.

```text
Destination

↓

Where To Go

-------------------------

Arguments

↓

What The Destination Needs To Know
```

The same destination can display thousands of different pieces of data.

For example,

one Product Details screen can display every product in the catalog.

Only the Product ID changes.

The destination remains the same.

---

## Common Misconception

A common misconception is:

```text
Arguments = Application State
```

Incorrect.

Arguments are only the information required to reach a destination.

They are not intended to hold large objects,

shared state,

or business data.

Arguments should remain small,

focused,

and specific to the navigation request.

---

## A Natural Question

We've learned how applications move between destinations

and pass information during navigation.

A natural question appears.

```text
How Does Android Remember Where We've Been So That The Back Button Returns To The Previous Destination?
```

That leads us to the next concept.

```text
Back Stack
```

---

## Revision

### Core Idea

```text
Navigation = Move To A Destination Possibly Carrying Arguments
```

---

### Mental Model

```text
Current Destination

↓

Navigation Request

↓

Destination + Arguments

↓

New Current Destination
```

The destination tells the application where to go.

The arguments provide the information needed after arriving.

---

### Production Recognition

When reading a production Android application,

look for navigation requests that specify:

- a destination,
- and sometimes arguments.

The navigation system is responsible for delivering both to the destination.

---

### Previous Concept

```text
NavHost
```

---

### Next Concept

```text
Back Stack
```
