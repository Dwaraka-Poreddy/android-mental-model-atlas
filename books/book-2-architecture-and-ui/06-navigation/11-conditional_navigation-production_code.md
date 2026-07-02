# Conditional Navigation — Production Code

## Purpose

Understand how production Android applications decide whether navigation should proceed immediately or be redirected based on the current application state.

---

## Looking Back

In the previous chapter, we learned that navigation is often a decision rather than a direct action.

Production Android applications evaluate the application's current state before deciding whether to navigate, redirect, or postpone the request.

This chapter focuses on the implementation patterns you'll commonly encounter.

---

# Recognition

When exploring a production Android project, you'll often see navigation decisions happening before `navController.navigate(...)` is called.

Typical patterns include:

```kotlin
if (!sessionManager.isLoggedIn()) {
    ...
}
```

```kotlin
if (!onboardingCompleted) {
    ...
}
```

```kotlin
when (userRole) {
    ...
}
```

```kotlin
featureFlag.isEnabled(...)
```

or

```kotlin
navigationGuard.canNavigate(...)
```

These are strong indicators that navigation depends on application state rather than user actions alone.

---

# Pattern 1 — Authentication Guard

The most common example is authentication.

```text
Profile Requested

↓

User Logged In?

├── Yes → Profile

└── No → Login
```

Typical production code:

```kotlin
if (sessionManager.isLoggedIn()) {
    navigator.openProfile()
} else {
    navigator.openLogin()
}
```

The user isn't denied access forever.

Navigation is simply redirected until authentication succeeds.

---

# Pattern 2 — Onboarding Flow

Many applications require onboarding before allowing access to the main experience.

```text
App Launch

↓

Onboarding Completed?

├── Yes → Home

└── No → Onboarding
```

Once onboarding finishes, future launches go directly to the Home screen.

---

# Pattern 3 — Premium Features

Some destinations are available only to subscribed users.

```text
Premium Feature

↓

Subscription Active?

├── Yes → Feature

└── No → Upgrade
```

Notice that the destination doesn't perform the check.

The navigation layer decides where the user should go.

---

# Pattern 4 — Feature Flags

Large companies frequently release features gradually.

```text
Feature Enabled?

├── Yes → New Feature

└── No → Existing Flow
```

Feature flags allow teams to enable or disable navigation without publishing a new application version.

---

# Pattern 5 — Deep Links + Authentication

A common production scenario combines Deep Linking with authentication.

Imagine the user opens:

```text
https://shop.com/orders/123
```

If the user isn't signed in, the application doesn't discard the request.

Instead, it typically follows this flow.

```text
Deep Link

↓

Login

↓

Original Destination
```

The intended destination is remembered and resumed after authentication succeeds.

---

# Pattern 6 — Maintenance Mode

Applications sometimes disable specific areas temporarily.

```text
Payments

↓

Maintenance Mode?

├── No → Payments

└── Yes → Maintenance Screen
```

This prevents users from entering features that are temporarily unavailable.

---

# Pattern 7 — Permission Gates

Some destinations require Android permissions.

For example,

```text
Camera

↓

Camera Permission?

├── Granted → Camera

└── Missing → Permission Request
```

The permission request becomes part of the navigation flow.

---

# Pattern 8 — Splash Routing

Many applications make their first navigation decision during startup.

```text
Splash

↓

Check Session

↓

Check Onboarding

↓

Check App Version

↓

Navigate
```

The Splash screen acts as a coordinator rather than a destination the user interacts with.

---

# How To Read Production Code

When you see:

```kotlin
if (!sessionManager.isLoggedIn())
```

read it as:

```text
Authentication Gate
```

---

When you see:

```kotlin
featureFlag.isEnabled(...)
```

read it as:

```text
Conditional Feature Access
```

---

When you see:

```kotlin
navigator.openLogin()
```

immediately followed by later navigation,

read it as:

```text
Navigation Has Been Deferred Until A Condition Is Met
```

---

# Common Production Mistakes

## Checking Conditions Inside Every Screen

Avoid placing authentication checks inside each destination.

Instead, decide whether navigation should occur before opening the screen.

---

## Forgetting The Original Destination

If a user is redirected to Login, remember where they originally wanted to go.

After successful authentication, continue to the original destination whenever appropriate.

---

## Mixing Business Logic With Navigation

The navigation layer decides *where* the user should go.

Business logic decides *whether* the required condition is satisfied.

Keep these responsibilities separate.

---

## Hardcoding Navigation Decisions

Avoid scattering conditional checks throughout the application.

Centralizing navigation decisions makes behavior easier to understand and maintain.

---

# Key Takeaways

```text
✓ Production applications often evaluate application state before navigating.

✓ Authentication is only one example of conditional navigation.

✓ Onboarding, feature flags, subscriptions, permissions, and maintenance mode commonly influence navigation.

✓ Deep Links often pause for authentication before continuing.

✓ Navigation decisions should be centralized whenever possible.

✓ Conditional navigation is about deciding the correct destination based on the current application state.
```