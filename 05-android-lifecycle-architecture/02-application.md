# Application

## Looking Back

In the previous chapter we learned:

```text
User Taps App Icon

↓

Android Creates Process

↓

Running Application
```

A natural question appears:

```text
Once The Process Is Created, What Is The First AndroidxComponent That Comes To Life?
```

The answer is:

```text
Application
```

---

## Application

The `Application` object represents:

```text
The Entire Running Application
```

It is created when the Android Process starts and exists until that process is destroyed.

Conceptually:

```text
Android Process

↓

Application

↓

Activities

↓

ViewModels

↓

UI
```

Every screen in the application exists under the same Application object.

---

## A Simple Observation

Consider a food delivery application.

Before any screen is shown, the application may need to:

```text
Initialize Logging

↓

Initialize Analytics

↓

Initialize Crash Reporting
```

These tasks belong to:

```text
The Entire Application
```

not to an individual screen.

If every Activity performed the same initialization:

```text
Home Activity

↓

Initialize Everything

-------------------------

Profile Activity

↓

Initialize Everything

-------------------------

Orders Activity

↓

Initialize Everything
```

the work would be duplicated.

Instead:

```text
Application Starts

↓

Initialize Once

↓

All Screens Use It
```

---

## Minimal Code

```kotlin
class MyApplication : Application()
```

This class becomes the application-level owner for work that belongs to the entire app.

---

## Production Code

```kotlin
class FoodDeliveryApplication : Application() {

    override fun onCreate() {
        super.onCreate()

        setupLogging()
        setupAnalytics()
        setupCrashReporting()
    }
}
```

### How To Read It

```text
FoodDeliveryApplication

↓

Owns The Entire Application

----------------------------

onCreate()

↓

Runs When Application Starts

----------------------------

setupLogging()

↓

Application-wide Setup

----------------------------

setupAnalytics()

↓

Application-wide Setup

----------------------------

setupCrashReporting()

↓

Application-wide Setup
```

You do not need to know the implementation details.

Simply recognize that this is:

```text
Application-level Initialization
```

---

## Production Notes

```text
• Most Android applications have one Application object.

• It usually lives longer than every Activity and ViewModel.

• Work that belongs to the entire application is typically initialized here.

• Screen-specific logic should not be placed inside the Application class.
```

---

## Another Common Misconception

A common misconception is:

```text
Application = Every Global Variable
```

Not necessarily.

The Application object represents:

```text
Application-level Ownership
```

It is not intended to become a place where unrelated data is stored.

---

## Revision

### Core Idea

```text
Application = Owner Of The Running Application
```

### Mental Model

```text
Android Process

↓

Application

↓

Activities

↓

ViewModels

↓

UI
```

### Production Recognition

```text
Application.onCreate()

↓

Initialize Once

↓

Used By Entire App
```

### Previous Concept

```text
Android Process
```

### Next Concept

```text
Activity
```
