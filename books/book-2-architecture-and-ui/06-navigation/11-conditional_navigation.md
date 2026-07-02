<!--
Primary Question: Should every navigation request always succeed?
Prerequisites: Multi-Module Navigation
After Reading: You understand that navigation often depends on application state and that applications may redirect users before allowing them to reach their intended destination.
Next Concept: Navigation + ViewModel Scoping
-->

# Conditional Navigation

## Looking Back

So far, we've assumed that whenever a feature requests navigation, the destination opens immediately.

A natural question appears.

```text
Should Every Navigation Request Always Succeed?
```

---

## The Problem

Imagine a user taps the **Profile** button.

The application first checks whether the user is signed in.

```text
Open Profile

↓

User Logged In?

↓

No
```

Should the application still open the Profile screen?

Probably not.

Instead, it should guide the user through the Login screen first.

```text
Profile Requested

↓

Login

↓

Profile
```

The original destination hasn't changed.

The application simply needs to satisfy a condition before allowing navigation to continue.

---

## A Thought Experiment

Imagine boarding a flight.

You know exactly where you're travelling.

Your ticket is valid.

Your destination hasn't changed.

However, before you can board, the airline checks a few conditions.

```text
Boarding Pass

Passport

Security Check
```

Only after those checks succeed are you allowed onto the aircraft.

The destination never changed.

The journey was simply delayed until the required conditions were satisfied.

Navigation works in exactly the same way.

Sometimes the destination is correct, but the application must first verify that the user is allowed to reach it.

---

## Conditional Navigation

Conditional Navigation means deciding whether navigation should proceed based on the current state of the application.

Conceptually,

```text
Navigation Request

↓

Check Application State

↓

Condition Satisfied?

├── Yes → Navigate

└── No → Redirect
```

Instead of navigating immediately, the application first evaluates whether any prerequisites must be met.

---

## Common Examples

Authentication is the most common example.

```text
Open Profile

↓

Not Logged In

↓

Login

↓

Profile
```

However, many other conditions can affect navigation.

For example,

```text
Open Premium Feature

↓

Subscription Active?

↓

No

↓

Upgrade
```

---

```text
Open Payments

↓

Maintenance Mode?

↓

Yes

↓

Maintenance Screen
```

---

```text
Open Dashboard

↓

Onboarding Complete?

↓

No

↓

Onboarding
```

Although the conditions are different, the navigation flow follows the same pattern.

---

## Why This Matters

Applications become easier to reason about when navigation decisions are based on application state instead of being scattered throughout individual screens.

The destination doesn't decide whether it should be opened.

The application decides before navigation occurs.

This keeps responsibilities clear and provides a more consistent user experience.

---

## Common Misconception

A common misconception is:

```text
Conditional Navigation = Authentication
```

Incorrect.

Authentication is only one example.

Any application state can influence navigation.

Examples include:

- onboarding completion,
- premium subscriptions,
- user roles,
- feature flags,
- maintenance mode,
- permissions,
- or application updates.

The principle remains the same.

---

## A Natural Question

We've learned that navigation decisions often depend on application state.

A natural question appears.

```text
When Navigation Finally Opens A Screen,

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
Conditional Navigation = Checking Application State Before Navigation Continues
```

---

### Mental Model

```text
Navigation Request

↓

Check Conditions

↓

Satisfied?

├── Yes → Navigate

└── No → Redirect
```

The destination doesn't change.

The path to the destination may.

---

### Production Recognition

When thinking about Conditional Navigation, remember that navigation is often a decision rather than a direct action.

Applications frequently evaluate their current state before deciding whether to continue, redirect, or delay navigation.

---

## Looking Ahead

This chapter explained **why** applications sometimes redirect users before allowing navigation to continue.

In the companion document,

**Conditional Navigation — Production Code**,

we'll explore how production Android applications implement conditional navigation using:

- Authentication guards
- Splash routing
- Onboarding flows
- Feature flags
- Premium feature gates
- Maintenance mode
- Deep Links with authentication
- Pending destinations
- Common production patterns

---

### Previous Concept

```text
Multi-Module Navigation
```

---

### Next Concept

```text
Navigation + ViewModel Scoping
```