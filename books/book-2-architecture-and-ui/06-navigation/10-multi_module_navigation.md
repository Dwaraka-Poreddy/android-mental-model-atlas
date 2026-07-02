<!--
Primary Question: How can independent features navigate to one another without becoming tightly coupled?
Prerequisites: Nested Navigation Graphs
After Reading: You understand why large applications avoid direct dependencies between features and how navigation contracts keep features independent.
Next Concept: Navigation + ViewModel Scoping
-->

# Multi-Module Navigation

## Looking Back

In the previous chapter, we learned that large applications organize their navigation into feature-specific graphs.

A natural question appears.

```text
If Each Feature Owns Its Own Navigation Graph,
How Can One Feature Navigate To Another Without Knowing Its Internal Implementation?
```

---

## The Problem

Imagine an application with three features.

```text
Authentication

Shopping

Profile
```

The Shopping feature needs to open the Profile screen.

A straightforward implementation might look like this.

```kotlin
ProfileScreen(
    userId = currentUser.id
)
```

This works.

A few months later, the Profile team adds a new requirement.

The Profile screen now needs both a `userId` and a `profileType`.

```kotlin
ProfileScreen(
    userId = currentUser.id,
    profileType = ProfileType.PUBLIC
)
```

Suddenly, every place that opens the Profile screen must be updated.

The Shopping feature changes.

The Search feature changes.

The Notifications feature changes.

The Settings feature changes.

None of these features actually changed their own behavior. They only changed because the Profile feature changed.

This is called **tight coupling**.

As more features begin depending directly on one another, even small changes can ripple throughout the application, making it harder to develop and maintain.

---

## A Thought Experiment

Imagine you're using a payment gateway.

From your application's perspective, making a payment is as simple as:

```text
Process Payment
```

You don't know:

- which bank processes the payment,
- which servers are involved,
- or how fraud detection works.

You only know the contract:

```text
Process Payment(amount)
```

As long as that contract stays the same, the payment provider is free to change everything behind the scenes without affecting your application.

Navigation should work the same way.

The Shopping feature shouldn't know which screen the Profile feature uses, what arguments it requires internally, or how it is implemented.

It should simply request:

```text
Open Profile
```

The navigation system is responsible for translating that request into the appropriate destination.

This keeps features independent and allows each feature to evolve without forcing changes throughout the rest of the application.

---

## Multi-Module Navigation

Multi-module navigation is an architectural approach where features communicate through navigation without depending directly on each other's implementation.

Conceptually, instead of:

```text
Shopping

↓

ProfileScreen()
```

applications move toward:

```text
Shopping

↓

Navigation Contract

↓

Profile Feature
```

The Shopping feature doesn't know how the Profile feature works. It only knows that it wants to navigate there.

---

## Why This Matters

Large applications are often developed by multiple teams, with each team owning one or more features.

If every feature depends directly on every other feature, small changes can ripple throughout the application. Keeping navigation independent allows each feature to evolve with minimal impact on others, making applications easier to develop, maintain, and scale.

---

## Common Misconception

A common misconception is:

```text
Multi-Module Navigation = Using Multiple Gradle Modules
```

Incorrect.

Gradle modules are one way to organize code, but they are not the concept itself.

Multi-module navigation is about reducing coupling between features. The same architectural principle applies even if the application isn't physically divided into multiple modules.

The goal is feature independence.

---

## A Natural Question

We've learned how features can navigate without tightly depending on one another.

A natural question appears.

```text
When A Destination Is Opened,
Who Owns Its ViewModel?
And How Long Does It Live?
```

That leads us to the next concept.

```text
Navigation + ViewModel Scoping
```

---

## Revision

### Core Idea

```text
Multi-Module Navigation = Navigating Between Features Without Direct Feature Dependencies
```

---

### Mental Model

```text
Feature

↓

Navigation Contract

↓

Destination Feature
```

The caller requests navigation.

The navigation system resolves the destination.

The two features remain independent.

---

### Production Recognition

When thinking about multi-module navigation, remember that the goal isn't simply to split an application into modules.

The goal is to keep features independent by communicating through navigation contracts rather than direct implementation details.

---

## Looking Ahead

This chapter explained **why** large applications avoid direct navigation dependencies between features.

In the companion document, **Multi-Module Navigation — Production Code**, we'll explore how production Android applications implement this using:

- Feature modules
- Navigation contracts
- Navigator interfaces
- Dependency inversion
- Feature APIs
- Dynamic feature modules
- Cross-module routing
- Common production architectures

---

### Previous Concept

```text
Nested Navigation Graphs
```

---

### Next Concept

```text
Navigation + ViewModel Scoping
```
