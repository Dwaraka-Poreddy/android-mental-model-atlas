# FragmentManager

## Looking Back

In the previous chapter we learned:

```text
Activity

↓

Fragment

↓

Part Of A Screen
```

A natural question appears:

```text
If Fragments Represent Parts Of A Screen,

Who Creates Them?

Who Removes Them?

Who Replaces Them?
```

---

## A Simple Observation

Imagine a shopping application.

Initially, the screen displays:

```text
Product Details
```

The user taps:

```text
Reviews
```

The screen should now display:

```text
Reviews
```

The `ReviewsFragment` does not appear by itself.

Something must coordinate this change.

---

## FragmentManager

`FragmentManager` is responsible for:

```text
Creating

↓

Adding

↓

Replacing

↓

Removing Fragments
```

Conceptually:

```text
Activity

↓

FragmentManager

↓

Fragments
```

The Activity owns the screen.

The FragmentManager owns the Fragments that make up that screen.

---

## A Natural Question

If FragmentManager manages Fragments,

how does it describe a change?

For example:

```text
Current Fragment

↓

Replace

↓

New Fragment
```

Android represents this change as a:

```text
Fragment Transaction
```

---

## Fragment Transaction

A transaction is simply:

```text
A Set Of Changes To Fragments
```

Conceptually:

```text
Current Fragment

↓

Replace

↓

Reviews Fragment
```

or

```text
No Fragment

↓

Add

↓

Product Fragment
```

or

```text
Current Fragment

↓

Remove

↓

Empty Container
```

---

## Another Natural Question

Suppose the user performs:

```text
Product Details

↓

Reviews

↓

Specifications
```

Now the user presses:

```text
Back
```

How does Android know where to return?

---

## Back Stack

FragmentManager remembers previous Fragment states using the:

```text
Back Stack
```

Conceptually:

```text
Product Details

↓

Reviews

↓

Specifications

↓

Back

↓

Reviews

↓

Back

↓

Product Details
```

This allows Android to restore previously displayed Fragments.

---

## Minimal Code

```kotlin
supportFragmentManager
    .beginTransaction()
    .replace(
        R.id.container,
        ReviewsFragment()
    )
    .addToBackStack(null)
    .commit()
```

You do not need to understand every API yet.

Focus on recognizing the intent.

---

## Production Code

```kotlin
supportFragmentManager
    .beginTransaction()
    .replace(
        R.id.container,
        ReviewsFragment()
    )
    .addToBackStack(null)
    .commit()
```

### How To Read It

Read it as:

```text
FragmentManager

↓

Start Screen Change

↓

Replace Current Fragment

↓

Remember Previous Fragment

↓

Apply Change
```

rather than:

```text
beginTransaction()

↓

replace()

↓

addToBackStack()

↓

commit()
```

The first interpretation focuses on behavior rather than API names.

---

## Production Notes

```text
• Every Activity owns a FragmentManager.

• FragmentManager is responsible for managing Fragments.

• A Transaction describes one or more Fragment changes.

• The Back Stack enables Back navigation between Fragment states.

• Modern Compose applications often use Navigation Compose instead of FragmentManager,
but existing Android codebases still rely heavily on FragmentManager.
```

---

## Another Common Misconception

A common misconception is:

```text
Fragment Manages Itself
```

Incorrect.

```text
Activity

↓

FragmentManager

↓

Fragment
```

The FragmentManager is responsible for creating and changing Fragments.

---

## Putting Everything Together

```text
Android Process

↓

Application

↓

Activity

↓

FragmentManager

↓

Fragment
```

Each level owns and manages the level below it.

---

## Revision

### Core Idea

```text
FragmentManager = Manager Of Fragments
```

### Mental Model

```text
Activity

↓

FragmentManager

↓

Fragments
```

### Transaction

```text
Change One Or More Fragments
```

### Back Stack

```text
Remember Previous Fragment States
```

### Production Recognition

```text
supportFragmentManager

↓

Manage Fragments

↓

Change Screen

↓

Remember Previous State
```

### Previous Concept

```text
Fragment
```

### Next Concept

```text
Lifecycle
```
