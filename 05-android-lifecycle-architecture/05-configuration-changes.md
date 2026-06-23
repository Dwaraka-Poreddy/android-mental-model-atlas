# Configuration Changes

## Looking Back

In the previous chapter we learned:

```text
Activity

↓

Lifecycle

↓

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

A natural assumption is:

```text
Activity Destroyed

↓

User Closed The Screen
```

But that is not always true.

---

## A Simple Observation

Imagine searching for a restaurant.

You type:

```text
Chicken Biryani
```

Now you rotate the device.

A moment later:

```text
The Screen Appears Again
```

even though:

```text
You Never Closed The App
```

A natural question appears:

```text
Why Did Android Restart My Screen?
```

---

## Why Does Android Do This?

Sometimes the environment around the screen changes.

For example:

```text
Portrait

↓

Landscape
```

or

```text
Light Mode

↓

Dark Mode
```

or

```text
Small Font

↓

Large Font
```

The existing screen was created for the old environment.

Instead of modifying every UI element individually,

Android simply creates a new screen that matches the new environment.

Android calls these situations:

```text
Configuration Changes
```

---

## Configuration Changes

A Configuration Change is:

```text
A Change In The Environment That Causes Android To Recreate The Activity
```

Conceptually:

```text
Old Activity

↓

Destroyed

↓

New Activity

↓

Created
```

The application is still running.

Only the Activity is recreated.

---

## Android Example

Suppose the user:

```text
Open App

↓

Browse Restaurants

↓

Rotate Device
```

Android performs:

```text
Old Activity

↓

onDestroy()

↓

New Activity

↓

onCreate()
```

The user never closed the application.

Android simply created a new Activity for the updated environment.

---

## Production Code

```kotlin
class HomeActivity : ComponentActivity() {

    override fun onCreate(
        savedInstanceState: Bundle?
    ) {
        super.onCreate(savedInstanceState)
    }

    override fun onDestroy() {
        super.onDestroy()
    }

}
```

### How To Read It

When the user rotates the device:

```text
Old Activity

↓

onDestroy()

↓

New Activity

↓

onCreate()
```

Do not interpret this as:

```text
User Closed The App
```

Instead read it as:

```text
Android Recreated The Screen
```

to match a new environment.

---

## Production Notes

```text
• Device rotation is only one example of a configuration change.

• Language, font scale and UI mode changes may also recreate Activities.

• The Android Process usually continues running.

• Only the Activity is recreated.
```

---

## Another Common Misconception

A common misconception is:

```text
Activity Destroyed = Application Closed
```

Incorrect.

```text
Android Process

↓

Still Running

-------------------------

Old Activity

↓

Destroyed

↓

New Activity

↓

Created
```

The application continues to exist.

Only the screen has been replaced.

---

## Putting Everything Together

```text
Android Process

↓

Application

↓

Activity

↓

Configuration Change

↓

Old Activity Destroyed

↓

New Activity Created
```

---

## A Natural Question

Now imagine the user types:

```text
Chicken Biryani
```

into a search box.

Then rotates the device.

```text
Old Activity

↓

Destroyed

↓

New Activity

↓

Created

↓

Search Box Empty
```

The user's screen state is lost.

Because the search text existed inside:

```text
Old Activity
```

When the configuration changed:

```text
Old Activity

↓

Destroyed
```

everything owned by that Activity was destroyed with it.

Android then created:

```text
New Activity
```

which starts as a completely new object.

Conceptually:

```text
Old Activity

Search = "Chicken Biryani"

↓

Destroyed

↓

New Activity

Search = Empty
```

The new Activity has no knowledge of what existed inside the old one.

As a result, the user's screen state appears to be lost.

A natural question appears:

```text
Where Should Screen State Live So That It Survives Activity Recreation?
```

That question leads us to the next chapter:

```text
ViewModel
```

---

## Revision

### Core Idea

```text
Configuration Change

=

Environment Change That Recreates The Activity
```

### Mental Model

```text
Old Activity

↓

Destroyed

↓

New Activity

↓

Created
```

### Production Recognition

```text
onDestroy()

↓

onCreate()

↓

Screen Recreated Not Application Closed
```

### Previous Concept

```text
Lifecycle
```

### Next Concept

```text
ViewModel
```
