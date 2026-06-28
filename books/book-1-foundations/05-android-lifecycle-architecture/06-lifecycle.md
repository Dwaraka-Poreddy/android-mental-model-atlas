# Lifecycle

## Looking Back

In the previous chapter we learned:

```text
Android Process

↓

Application

↓

Activity

↓

One Screen
```

A natural question appears:

```text
A Screen Does Not Simply Exist Forever. So How Does Android Know What Is Happening To It?
```

---

## A Simple Observation

Imagine using a food delivery application.

You:

```text
Open The App

↓

Browse Restaurants

↓

Lock The Phone

↓

Unlock The Phone

↓

Receive A Call

↓

Return To The App

↓

Leave The App
```

From the user's perspective,

the same screen goes through many different situations.

Android needs a way to know about these changes.

---

## Why Does Android Care?

Different situations require different behavior.

For example:

```text
User Starts Watching Video

↓

Video Should Play

-------------------------

User Receives A Phone Call

↓

Video Should Pause

-------------------------

User Closes The Screen

↓

Video Resources Should Be Released
```

Android needs a way to inform the application about these changes so that it can respond appropriately.

Lifecycle is that mechanism.

---

## Lifecycle

Lifecycle represents:

```text
The Different Stages A Screen Goes Through During Its Lifetime
```

Conceptually:

```text
Created

↓

Visible

↓

Interactive

↓

Background

↓

Destroyed
```

Instead of continuously checking the screen,

Android simply informs the Activity whenever it moves from one stage to another.

---

## Minimal Code

```kotlin
override fun onCreate(...) { }

override fun onStart() { }

override fun onResume() { }

override fun onPause() { }

override fun onStop() { }

override fun onDestroy() { }
```

These callbacks correspond to different stages in the Activity's lifetime.

---

## Android Example

Suppose the user performs the following actions:

```text
Open App

↓

Use Screen

↓

Press Home

↓

Return To App

↓

Close App
```

Android observes these changes and notifies the Activity accordingly.

Conceptually:

```text
Screen Created

↓

Screen Visible

↓

Screen Interactive

↓

Screen Moves To Background

↓

Screen Destroyed
```

The Activity always knows what is happening to it.

---

## Production Code

```kotlin
class HomeActivity : ComponentActivity() {

    override fun onCreate(
        savedInstanceState: Bundle?
    ) {
        super.onCreate(savedInstanceState)
    }

    override fun onResume() {
        super.onResume()
    }

    override fun onPause() {
        super.onPause()
    }
}
```

### How To Read It

Don't read this as:

```text
Three Android APIs
```

Read it as:

```text
onCreate()

↓

Screen Is Created

-------------------------

onResume()

↓

User Can Interact With The Screen

-------------------------

onPause()

↓

User Is Leaving The Screen
```

The callbacks simply describe events happening to the Activity.

---

## Production Notes

```text
• Lifecycle callbacks are notifications from Android.

• Activities move through multiple lifecycle stages during normal use.

• Most production applications perform different work depending on the current lifecycle state.

• Many modern Android APIs are lifecycle-aware and automatically respond to these state changes.
```

---

## Another Common Misconception

A common misconception is:

```text
Lifecycle = A List Of Callbacks
```

Not quite.

Lifecycle is actually:

```text
The Lifetime Of A Screen
```

The callbacks are simply Android's way of informing the Activity about those changes.

---

## Putting Everything Together

```text
Android Process

↓

Application

↓

Activity

↓

Lifecycle

    Created

    ↓

    Visible

    ↓

    Interactive

    ↓

    Background

    ↓

    Destroyed
```

Android keeps the Activity informed as it moves through these stages.

---

## Revision

### Core Idea

```text
Lifecycle = The Different Stages Of A Screen's Lifetime
```

### Mental Model

```text
Created

↓

Visible

↓

Interactive

↓

Background

↓

Destroyed
```

### Production Recognition

```text
onCreate()

↓

Screen Created

-------------------------

onResume()

↓

User Interacting

-------------------------

onPause()

↓

User Leaving
```

### Previous Concept

```text
Activity
```

### Next Concept

```text
Configuration Changes
```
