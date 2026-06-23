# Activity

## Looking Back

In the previous chapter we learned:

```text
Android Process

↓

Application

↓

Entire Running Application
```

A natural question appears:

```text
If The Application Represents The Entire App, How Does Android Represent One Screen?
```

The answer is:

```text
Activity
```

---

## Activity

An Activity represents:

```text
One Screen Of An Android Application
```

Conceptually:

```text
Android Process

↓

Application

↓

Activity

↓

Screen
```

Each visible screen is represented by an Activity.

---

## A Simple Observation

Consider a food delivery application.

The user may visit:

```text
Home

↓

Restaurant

↓

Cart

↓

Profile
```

Although all of these belong to the same application,

they are different screens.

Android represents each screen using an Activity.

---

## Minimal Code

```kotlin
class MainActivity : ComponentActivity()
```

This declares an Activity named `MainActivity`.

---

## Production Code

```kotlin
class HomeActivity : ComponentActivity() {

    override fun onCreate(
        savedInstanceState: Bundle?
    ) {
        super.onCreate(savedInstanceState)

        setContent {
            HomeScreen()
        }
    }
}
```

### How To Read It

```text
HomeActivity

↓

Represents The Home Screen

-------------------------

onCreate()

↓

Activity Is Being Created

-------------------------

setContent()

↓

Display UI

-------------------------

HomeScreen()

↓

UI For This Screen
```

You do not need to understand every API yet.

Simply recognize the responsibility of each part.

---

## Production Notes

```text
• An Activity represents a screen.

• Modern Compose applications often use a single Activity.

• Navigation usually changes the displayed UI rather than creating many Activities.

• Business logic typically belongs in a ViewModel, not in an Activity.
```

---

## Another Common Misconception

A common misconception is:

```text
Application = Screen
```

Incorrect.

```text
Application

↓

Entire Running App

-------------------------

Activity

↓

One Screen
```

An application may contain one or many Activities.

---

## Revision

### Core Idea

```text
Activity = One Screen Of An Android Application
```

### Mental Model

```text
Android Process

↓

Application

↓

Activity

↓

Screen
```

### Production Recognition

```text
class HomeActivity

↓

Owns The Home Screen
```

### Previous Concept

```text
Application
```

### Next Concept

```text
Fragment
```
