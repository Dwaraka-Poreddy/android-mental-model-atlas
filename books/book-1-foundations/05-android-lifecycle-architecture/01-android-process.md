# Android Process

## Looking Back

In DOC 1 we learned:

```text
Program

↓

Process

↓

Thread
```

We discovered that:

```text
A Program Is Code Stored On Disk
```

while

```text
A Process Is A Running Program
```

Now we enter the Android world.

A natural question appears:

```text
When I Tap An App Icon, What Actually Starts Running?
```

---

## Android Process

When an Android application needs to run,

Android creates a process for it.

Conceptually:

```text
APK

↓

Android Process

↓

Running Application
```

The APK contains the application files.

The Android Process is the live instance executing in memory.

Everything that happens inside the application happens inside this process.

---

## Android Example

Suppose the user taps:

```text
Food Delivery App
```

Android performs the following steps:

```text
User Taps Icon

↓

Android Creates Process

↓

Application Starts

↓

Activity Starts

↓

Screen Appears
```

The process becomes the container for the running application.

---

## Production Code

Consider the following production code.

```kotlin
class UserViewModel : ViewModel() {

    ...

}
```

When an experienced Android engineer reads this, they don't see an isolated class.

They mentally place it inside the application hierarchy:

```text
Android Process

↓

Application

↓

Activity

↓

ViewModel
```

Production Notes:

```text
• Most Android applications run inside a single process.

• Android is responsible for creating and destroying that process.

• Activities, ViewModels, coroutines, Flows and Compose all execute inside the same running process.
```

---

## Another Common Misconception

A common misconception is:

```text
APK

=

Running Application
```

Incorrect.

```text
APK

↓

Application Files

-------------------------

Android Process

↓

Running Application
```

One is stored on disk.

The other exists in memory while the application is executing.

---

## Revision

### Core Idea

```text
Android Process = Running Instance Of An Android Application
```

### Mental Model

```text
APK

↓

Android Process

↓

Running Application
```

### Production Recognition

```text
ViewModel

↓

Lives Inside An Android Process
```

### Previous Concept

```text
Process (DOC 1)
```

### Next Concept

```text
Application
```
