<!--
Primary Question: How does the navigation system know which screens exist and how they connect?
Prerequisites: Why Navigation Exists
After Reading: You understand that a Navigation Graph is a declarative blueprint describing all valid navigation paths in an application.
Next Concept: NavController
-->

# Navigation Graph

## Looking Back

In the previous chapter, we learned that applications use a dedicated navigation system to coordinate movement between screens.

Instead of allowing screens to communicate directly,

every screen requests navigation through a central system.

A natural question appears.

```text
How Does The Navigation System Know Which Screens Exist And How They Connect?
```

---

## The Problem

Imagine building an e-commerce application.

It contains these screens.

```text
Home

Search

Product Details

Cart

Checkout

Order Confirmation

Profile
```

Some transitions make perfect sense.

```text
Home → Search

Search → Product Details

Product Details → Cart

Cart → Checkout
```

Others don't.

```text
Checkout → Search

Order Confirmation → Cart

Profile → Checkout
```

Without a clear definition,

the navigation system has no idea which transitions are valid.

There must be a way to describe the application's navigation structure.

---

## A Thought Experiment

Imagine opening Google Maps.

The map already knows:

- every city,
- every road,
- and how they're connected.

The map doesn't drive your car.

It doesn't decide where you should go.

It simply describes what is possible.

```text
City A ───── City B
   │            │
   │            │
   └────── City C
```

A Navigation Graph works the same way.

It doesn't perform navigation.

It simply describes every valid path through the application.

---

## Navigation Graph

A Navigation Graph is the blueprint of an application's navigation.

Conceptually,

it describes two things.

```text
Destinations

+

Connections Between Them
```

It answers questions like:

```text
Can Home reach Search?

Can Cart reach Checkout?

Can Checkout reach Order Confirmation?
```

It does **not** answer:

```text
Should navigation happen now?
```

That decision belongs to another component.

The Navigation Graph only describes what is possible.

---

## Destinations

A destination is any screen the user can visit.

For example,

```text
Home

Search

Product Details

Cart

Checkout
```

Each destination represents a place the user can navigate to.

Nothing more.

Nothing less.

---

## Start Destination

Every Navigation Graph has one destination where the application begins.

Conceptually,

```text
Navigation Graph

↓

Start Destination

↓

Home
```

When navigation starts,

the navigation system begins at this destination.

Every journey through the application starts from here.

---

## Connections

A Navigation Graph also defines how destinations are connected.

Conceptually,

```text
Home

↓

Search

↓

Product Details

↓

Cart

↓

Checkout
```

Each connection represents a valid navigation path.

Together,

the destinations and their connections form the application's navigation map.

---

## A Declarative Blueprint

One important idea is that a Navigation Graph is **declarative**.

It describes

what is possible,

not

what will happen.

Think of it this way.

```text
Navigation Graph

↓

Possible Paths
```

Later,

another component will decide

which path should actually be taken.

---

## Why This Scales Better

As applications grow,

new screens are added over time.

Imagine introducing a new screen.

```text
Wishlist
```

The Navigation Graph simply gains one more destination

and the connections to reach it.

```text
Home

↓

Wishlist

↓

Product Details
```

The application's navigation remains organized in one place.

Instead of being scattered throughout the application,

the navigation structure is described by a single blueprint.

As the application grows,

the Navigation Graph grows with it,

making it easier to understand every possible navigation path.

---

## Common Misconception

A common misconception is:

```text
Navigation Graph = A List Of Screens
```

Incorrect.

A list only tells us what exists.

```text
Home

Search

Cart

Checkout
```

A graph also tells us how everything connects.

```text
Home

↓

Search

↓

Product Details

↓

Cart

↓

Checkout
```

The connections are just as important as the destinations themselves.

---

## A Natural Question

We now have a complete map of the application.

But another question immediately appears.

```text
The Map Exists.

Who Actually Reads It And Performs Navigation?
```

That responsibility belongs to the next concept.

```text
NavController
```

---

## Revision

### Core Idea

```text
Navigation Graph

=

The Blueprint Of Every Valid
Navigation Path In The Application
```

---

### Mental Model

```text
Navigation Graph

↓

Map

↓

Destinations + Connections
```

The graph describes what is possible.

It does not perform navigation.

---

### Production Recognition

When exploring a production Android project,

look for one place where all destinations

and their connections are defined.

Whether it's XML,

Compose,

or another navigation framework,

there is usually a single source of truth

describing the application's navigation structure.

---

### Previous Concept

```text
Why Navigation Exists
```

---

### Next Concept

```text
NavController
```
