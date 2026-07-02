<!--
Primary Question: How does Android remember previously visited destinations?
Prerequisites: Navigate & Arguments
After Reading: You understand that Android maintains a Back Stack to remember the user's navigation history and enable Back navigation.
Next Concept: Deep Linking
-->

# Back Stack

## Looking Back

In the previous chapter,

we learned how applications move between destinations.

```text
Current Destination

↓

Navigation Request

↓

New Destination
```

A natural question appears.

```text
Home

↓

Search

↓

Product Details

↓

Cart

-------------------------

How Does Android Remember This Journey?
```

---

## The Problem

Imagine using a shopping application.

You visit:

```text
Home

↓

Search

↓

Product Details

↓

Cart
```

Now you press the Back button.

The application returns to:

```text
Product Details
```

Press Back again.

```text
Search
```

Again.

```text
Home
```

How did Android know where to return?

The Navigation Graph doesn't remember your journey.

It only describes the possible paths.

The NavHost only displays the current destination.

So where is the navigation history stored?

---

## A Thought Experiment

Imagine stacking books on a table.

You place them down one by one.

```text
Book A
```

Then:

```text
Book B

──────────

Book A
```

Then:

```text
Book C

──────────

Book B

──────────

Book A
```

Every new book is placed on top.

Now remove the top book.

```text
Book B

──────────

Book A
```

The previous book is immediately visible again.

Android remembers navigation in a very similar way.

---

## Back Stack

The **Back Stack** is the navigation history maintained by Android.

Conceptually,

every time navigation moves to a new destination,

that destination is placed on top of the stack.

```text
Back Stack

↓

Current Destination

↓

Previous Destination

↓

Earlier Destination
```

The top of the stack always represents

the destination currently visible to the user.

---

## Building The Stack

Imagine the user starts here.

```text
Home
```

The Back Stack contains:

```text
Top

↓

Home
```

The user navigates to Search.

```text
Top

↓

Search

↓

Home
```

Then Product Details.

```text
Top

↓

Product Details

↓

Search

↓

Home
```

Then Cart.

```text
Top

↓

Cart

↓

Product Details

↓

Search

↓

Home
```

Every navigation request adds a new destination

to the top of the Back Stack.

---

## Going Back

Now the user presses Back.

The top destination is removed.

```text
Top

↓

Product Details

↓

Search

↓

Home
```

Press Back again.

```text
Top

↓

Search

↓

Home
```

Again.

```text
Top

↓

Home
```

Each Back action removes the current destination,

revealing the one beneath it.

---

## Why Is It Called A Stack?

Notice something interesting.

The last destination visited

is the first one removed.

```text
Last Visited

↓

First Removed
```

This behavior is called

**Last In, First Out (LIFO).**

A stack naturally follows this rule,

which is why Android stores navigation history this way.

---

## Navigation Graph vs Back Stack

It's important not to confuse these two concepts.

```text
Navigation Graph

↓

Describes All Possible Navigation Paths

-------------------------

Back Stack

↓

Remembers The User's Actual Journey
```

The Navigation Graph answers:

```text
Where Can I Go?
```

The Back Stack answers:

```text
Where Have I Been?
```

Together, they make navigation possible.

---

## Why This Matters

Earlier, we learned that the NavHost only displays the current destination.

Now we can complete the picture.

```text
NavHost

↓

Displays Current Destination

-------------------------

Back Stack

↓

Remembers Previous Destinations
```

The NavHost doesn't remember history.

The Back Stack does.

Each component has a different responsibility.

---

## Common Misconception

A common misconception is:

```text
The Back Stack Contains Every Screen Currently Displayed
```

Incorrect.

Only one destination is displayed at any time.

```text
NavHost

↓

Current Destination
```

The Back Stack simply remembers

the destinations that were visited,

allowing Android to return to them when Back is pressed.

---

## A Natural Question

We've learned how Android remembers

where the user has been.

A natural question appears.

```text
Navigation Usually Starts Inside The App.
But Can A User Jump Directly To A Destination From Outside The App?
```

That leads us to the next concept.

```text
Deep Linking
```

---

## Revision

### Core Idea

```text
Back Stack = The Navigation History Maintained As A Stack
```

---

### Mental Model

```text
Top

↓

Current Destination

↓

Previous Destination

↓

Earlier Destination
```

Every navigation request pushes a destination onto the stack. Every Bac action removes the top destination.

---

### Production Recognition

When reading a production Android application,

remember that navigation history is separate from navigation structure.

```text
Navigation Graph

↓

Possible Paths

-------------------------

Back Stack

↓

Actual Journey
```

The Navigation Graph defines where navigation can go.

The Back Stack remembers where the user has been.

---

### Previous Concept

```text
Navigate & Arguments
```

---

### Next Concept

```text
Deep Linking
```