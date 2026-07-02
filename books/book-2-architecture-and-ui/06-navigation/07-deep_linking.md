<!--
Primary Question: Can navigation begin from outside the application?
Prerequisites: Back Stack
After Reading: You understand what Deep Linking is, why it exists, and how it allows navigation to begin at a specific destination instead of following the normal in-app navigation flow.
Next Concept: Safe Args / Type-Safe Navigation
-->

# Deep Linking

## Looking Back

So far,

every navigation journey we've seen has started inside the application.

For example,

```text
Home

↓

Search

↓

Product Details

↓

Cart
```

The user launches the application,

then navigates from one destination to another.

A natural question appears.

```text
Can Navigation Start Somewhere Outside The Application?
```

---

## The Problem

Imagine a friend sends you this link.

```text
https://shop.com/products/42
```

You tap it.

Instead of opening the browser,

the shopping application opens directly to:

```text
Product Details
```

The application skipped:

```text
Home

↓

Search
```

and arrived immediately at the requested destination.

How is that possible?

There must be a way for navigation to begin from outside the application.

---

## A Thought Experiment

Imagine a large shopping mall.

Most visitors enter through the main entrance.

```text
Main Entrance

↓

Mall Directory

↓

Shop
```

But some places have dedicated entrances.

For example,

the supermarket may have an entrance from the parking lot,

or the cinema may have its own entrance from outside the building.

Visitors don't need to walk through the entire mall.

They arrive directly where they want to go.

Deep Linking works in a very similar way.

Normally,

navigation begins from the application's main entry point.

A Deep Link provides another entrance,

allowing navigation to begin directly at a specific destination.

---

## Deep Linking

Deep Linking is the ability to start navigation at a specific destination from outside the application.

Conceptually,

```text
Outside The Application

↓

Deep Link

↓

Specific Destination
```

Instead of beginning at the application's normal starting point,

navigation begins exactly where the user wants to go.

---

## Normal Navigation vs Deep Linking

Normal navigation begins inside the application.

```text
App Launch

↓

Home

↓

Search

↓

Product Details
```

Deep Linking begins somewhere outside the application.

```text
Email

↓

Product Details
```

or

```text
Notification

↓

Order Details
```

or

```text
QR Code

↓

Payment Screen
```

The destination is the same.

Only the starting point is different.

---

## Deep Links Are Everywhere

Most people use Deep Links every day without realizing it.

For example,

tapping:

- a product link in a messaging app,
- an order link in an email,
- a payment notification,
- a restaurant link in a food delivery app,
- or scanning a QR code,

often opens a specific screen inside an application,

instead of its Home screen.

These are all examples of Deep Linking.

---

## Why This Matters

Users don't always want to begin at the Home screen.

Sometimes,

they already know exactly where they want to go.

For example,

- a specific product,
- an order,
- a conversation,
- a payment screen,
- or another user's profile.

Deep Linking allows applications to take users directly to the information they requested,

making navigation faster and more convenient.

---

## Common Misconception

A common misconception is:

```text
Deep Linking = Opening The Application
```

Incorrect.

Opening the application is only part of the journey. The important idea is where navigation begins.

With a Deep Link, navigation begins directly at the requested destination, not necessarily at the application's normal entry point.

---

## A Natural Question

We've learned that Deep Links can navigate directly to a destination.

A natural question appears.

```text
If A Deep Link Can Carry Information, How Can That Information Be Passed Safely To The Destination?
```

That leads us to the next concept.

```text
Safe Args / Type-Safe Navigation
```

---

## Revision

### Core Idea

```text
Deep Linking = Starting Navigation At A Specific Destination From Outside The Application
```

---

### Mental Model

```text
Normal Navigation

↓

App Launch

↓

Home

↓

Destination

-------------------------

Deep Linking

↓

Outside The Application

↓

Destination
```

The destination remains the same.

Only the starting point changes.

---

### Production Recognition

When thinking about Deep Linking, remember one simple idea.

Applications don't always need to start from their Home screen.

Sometimes, navigation begins directly at the destination the user requested.

---

## Looking Ahead

This chapter focused on understanding **what** Deep Linking is.

In the companion document,

**Deep Linking — Production Code**,

we'll see how production Android applications implement Deep Links using:

- Intent Filters
- URI Schemes
- Android App Links
- Notifications
- Browser Links
- QR Codes
- Compose Navigation
- Testing Deep Links

---

### Previous Concept

```text
Back Stack
```

---

### Next Concept

```text
Safe Args / Type-Safe Navigation
```