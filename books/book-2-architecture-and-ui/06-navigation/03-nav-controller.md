<!--
Primary Question: Who actually performs navigation inside an Android application?
Prerequisites: Navigation Graph
After Reading: You understand that the NavController is responsible for reading the Navigation Graph, executing navigation requests, and managing navigation state.
Next Concept: NavHost
-->

# NavController

## Looking Back

In the previous chapter,

we learned that a Navigation Graph describes every valid navigation path in an application.

Conceptually,

it is the application's map.

A natural question appears.

```text
The Map Exists. Who Actually Reads It And Performs Navigation?
```

---

## The Problem

Imagine opening a navigation app.

A map is displayed on the screen.

The map already knows:

- every road,
- every intersection,
- every possible route.

But nothing moves.

Simply having a map doesn't take you anywhere.

Someone still needs to:

- decide when to start driving,
- choose which road to follow,
- and keep track of the current location.

A Navigation Graph has the same limitation.

It describes navigation.

It doesn't perform it.

There must be another component responsible for that.

---

## A Thought Experiment

Imagine driving through a city.

```text
Road Map

↓

Driver

↓

Destination
```

The map describes every possible road.

The driver reads the map,

decides where to go,

and actually drives the car.

Without the driver,

the map is just information.

Navigation in Android works the same way.

The Navigation Graph is the map.

The NavController is the driver.

---

## NavController

The NavController is responsible for performing navigation.

Conceptually,

its responsibilities are:

```text
Receive Navigation Requests

↓

Consult The Navigation Graph

↓

Move To The Requested Destination

↓

Remember The Current Navigation State
```

The Navigation Graph describes what is possible.

The NavController decides what actually happens.

---

## Moving Forward

When the user requests navigation,

the NavController moves to another destination.

Conceptually,

```text
Home

↓

Navigate

↓

Search
```

The request reaches the NavController.

The NavController follows the Navigation Graph and displays the requested destination.

---

## Moving Back

Navigation also works in the opposite direction.

When the user presses Back,

the NavController returns to the previous destination.

Conceptually,

```text
Search

↓

Back

↓

Home
```

The NavController remembers the user's journey and moves back through it when requested.

We'll learn exactly how this history is maintained when we study the Back Stack.

---

## The Relationship

By now, the navigation system consists of two parts.

```text
Navigation Graph

↓

Describes

↓

Possible Navigation Paths

-------------------------

NavController

↓

Performs

↓

Actual Navigation
```

One component defines the map.

The other follows it.

Together,

they make navigation possible.

---

## Why This Matters

Separating these responsibilities keeps the navigation system simple.

The Navigation Graph focuses on structure.

The NavController focuses on execution.

Neither component needs to perform the other's job.

This separation makes the navigation system easier to understand, maintain, and extend.

---

## Common Misconception

A common misconception is:

```text
The Navigation Graph Performs Navigation
```

Incorrect.

The Navigation Graph only describes the application's navigation structure.

The NavController is responsible for reading that structure and performing navigation.

Think of it this way.

```text
Navigation Graph = Map

-------------------------

NavController = Driver
```

A map never drives the car.

---

## A Natural Question

We now understand that the NavController performs navigation.

A natural question appears.

```text
The NavController Knows Which Screen Should Be Visible. But Where Is That Screen Actually Displayed?
```

That responsibility belongs to the next concept.

```text
NavHost
```

---

## Revision

### Core Idea

```text
NavController = The Component That Performs Navigation By Following The Navigation Graph
```

---

### Mental Model

```text
Navigation Graph

↓

Map

-------------------------

NavController

↓

Driver

-------------------------

Destination
```

The Navigation Graph describes navigation.

The NavController performs it.

---

### Production Recognition

When reading a production Android project,

look for a single component responsible for:

- receiving navigation requests,
- moving between destinations,
- and coordinating navigation.

That component is typically the NavController.

---

### Previous Concept

```text
Navigation Graph
```

---

### Next Concept

```text
NavHost
```
