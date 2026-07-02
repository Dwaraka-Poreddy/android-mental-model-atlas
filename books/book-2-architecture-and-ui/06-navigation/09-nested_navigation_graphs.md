<!--
Primary Question: How do applications organize navigation as they grow larger?
Prerequisites: Type-Safe Navigation
After Reading: You understand why large applications split navigation into multiple smaller graphs and how nested navigation graphs improve organization without changing navigation behavior.
Next Concept: Multi-Module Navigation
-->

# Nested Navigation Graphs

## Looking Back

So far, we've explored applications with only a handful of destinations.

A single navigation graph was enough to describe the entire application.

A natural question appears.

```text
What Happens When An Application Has 50, 100, Or Even 500 Destinations?
```

---

## The Problem

Imagine building a small shopping application.

Its navigation graph might look like this.

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

Everything fits comfortably into one graph.

Now imagine the application grows.

It now contains:

- Authentication
- Shopping
- Orders
- Payments
- Profile
- Settings
- Notifications
- Chat
- Help
- Wishlist

Trying to place every destination into one navigation graph quickly becomes overwhelming.

The graph becomes difficult to understand,

difficult to maintain,

and difficult to extend.

---

## A Thought Experiment

Imagine writing a small programming guide.

A single document is perfectly manageable.

Now imagine writing the entire Atlas.

Would everything live inside one gigantic Markdown file?

Probably not.

Instead,

the content is organized into books,

and each book is divided into smaller chapters.

```text
Atlas

↓

Book

↓

Chapter
```

Nothing changes about the knowledge itself.

We've simply organized it into smaller,

easier-to-understand pieces.

Large navigation graphs are organized in exactly the same way.

---

## Nested Navigation Graphs

A Nested Navigation Graph is simply a smaller navigation graph contained within a larger one.

Conceptually,

```text
Application

↓

Navigation Graph

↓

Feature Graph

↓

Destination
```

Instead of one giant graph,

the application is divided into feature-specific graphs.

---

## An Example

Rather than creating one enormous navigation graph,

the application can organize destinations like this.

```text
Authentication

↓

Login

Register

Forgot Password
```

---

```text
Shopping

↓

Home

Search

Product Details

Cart

Checkout
```

---

```text
Profile

↓

Profile

Edit Profile

Settings
```

Each feature owns its own navigation graph,

making the overall application easier to understand.

---

## Why This Matters

Splitting navigation into smaller graphs provides several benefits.

- Each feature becomes easier to understand.
- Teams can work on different features independently.
- Adding new destinations affects only one feature graph.
- Navigation becomes easier to maintain as the application grows.

Most importantly,

navigation itself doesn't change.

Only its organization does.

---

## Common Misconception

A common misconception is:

```text
Nested Navigation Graphs = A Different Way To Navigate
```

Incorrect.

Navigation works exactly the same way.

The application still moves between destinations.

Nested navigation graphs simply organize those destinations into smaller, more manageable groups.

---

## A Natural Question

We've learned how to organize navigation into feature-specific graphs.

A natural question appears.

```text
If Each Feature Has Its Own Navigation Graph,

Can Each Feature Also Live

In Its Own Module?
```

That leads us to the next concept.

```text
Multi-Module Navigation
```

---

## Revision

### Core Idea

```text
Nested Navigation Graphs = Organizing One Large Navigation Graph Into Smaller Feature Graphs
```

---

### Mental Model

```text
Application

↓

Navigation Graph

↓

Feature Graph

↓

Destination
```

Navigation remains the same.

Only the organization changes.

---

### Production Recognition

Large applications rarely maintain one enormous navigation graph.

Instead, they divide navigation into smaller graphs, each representing a single feature or user flow.

---

## Looking Ahead

This chapter explained **why** large applications organize navigation into nested graphs.

In the companion document,

**Nested Navigation Graphs — Production Code**,

we'll learn how Android implements nested graphs using:

- `navigation()` graphs
- Start destinations
- Graph routes
- Authentication flows
- Bottom navigation graphs
- Onboarding flows
- Graph-scoped ViewModels
- Common production patterns

---

### Previous Concept

```text
Type-Safe Navigation
```

---

### Next Concept

```text
Multi-Module Navigation
```