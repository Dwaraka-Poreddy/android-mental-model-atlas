# Why Background Work Exists

## Looking Back

In DOC 2 we learned coroutines —
suspend functions that run off the main thread.

In DOC 5 we learned `viewModelScope` —
a coroutine scope tied to the ViewModel's lifecycle.

With these tools we can do network calls and database queries
without blocking the UI.

```kotlin
viewModelScope.launch {
    repository.syncOrders()   // suspend fun — runs off main thread
}
```

This feels complete.

A natural question appears:

```text
If Coroutines Handle Off-Main-Thread Work,
Why Do We Need Anything Else?
```

---

## The Problem

Imagine an app that syncs order data every 15 minutes.

The user opens the app.

A coroutine starts in `viewModelScope`:

```kotlin
viewModelScope.launch {
    repository.syncOrders()
}
```

This works while the app is open.

Now the user swipes the app away.

```text
viewModelScope coroutine
        ↓
User swipes app away
        ↓
ViewModel is cleared
        ↓
viewModelScope is cancelled
        ↓
Sync stops halfway
        ↓
Data is partially written
        ↓
Database is in an inconsistent state
```

The sync was interrupted.

Orders may be half-saved.

The user reopens the app and sees stale data.

---

## It Gets Worse

On low-end devices, the OS kills background processes aggressively.

Even if the user does not swipe the app away,
the system can terminate the process at any time to free memory.

```text
Your app's process
        ↓
Android needs RAM
        ↓
Kills your process
        ↓
All coroutines die immediately
        ↓
Work stops — no warning, no cleanup
```

There is no way for a coroutine to prevent this.

A coroutine lives inside the process.

When the process dies, every coroutine dies with it.

---

## Another Scenario

The user taps "Upload photo."

They are on mobile data.

You want to upload only on WiFi to save their data allowance.

A `viewModelScope` coroutine cannot wait for WiFi.

```kotlin
viewModelScope.launch {
    // We are on mobile data right now.
    // We can't pause here and resume when WiFi appears.
    // We either upload now or cancel.
    uploadPhoto()
}
```

The coroutine has no way to say:
"pause me, and resume when WiFi becomes available."

It runs now or it cancels.

---

## The Core Tension

```text
viewModelScope coroutines are excellent for:

    Work triggered by user interaction
    Work that should stop when the user leaves
    Network calls, database queries, UI updates


viewModelScope coroutines fail for:

    Work that must complete even if the app closes
    Work that should wait for device conditions (WiFi, charging)
    Work that must survive device reboots
```

The ViewModel was designed to outlive screen rotation.

It was not designed to outlive the user leaving the app.

---

## The Mental Model

```text
Coroutines are tied to a scope.
Scopes are tied to lifecycle owners.
Lifecycle owners live inside the app's process.
The app's process can be killed.

Work that must survive app close
needs a system that lives outside the app's process.
```

---

## Two Different Tools For Two Different Jobs

```text
Need: Sync when WiFi available, even if app is closed

viewModelScope                  WorkManager
        ↓                             ↓
Lives with ViewModel            Lives with OS
        ↓                             ↓
Dies when app closes            Survives app close
        ↓                             ↓
Dies when process is killed     Survives process death
        ↓                             ↓
Can't wait for WiFi             Can wait for WiFi
        ↓                             ↓
Can't survive reboot            Survives device reboot
        ↓                             ↓
Wrong tool                      Right tool
```

---

## The Boundary

Choosing between the two is a question of intent:

```text
"I want this to run now, in the app."
        ↓
viewModelScope coroutine


"I want this to run eventually,
 even if the user has left the app."
        ↓
WorkManager
```

If the work only matters while the user is present —
coroutines are correct.

If the work must complete regardless of what the user does next —
WorkManager is the right tool.

---

## Production Notes

```text
* viewModelScope is cancelled in ViewModel.onCleared(), which is
  called when the user navigates away permanently. Any running
  coroutine will be cancelled without warning.

* The OS can kill your process at any time when it is in the
  background. There is no guaranteed grace period.

* Foreground services are a middle ground: they show a persistent
  notification and the OS gives them higher priority. But they
  still live in the process and cannot wait for conditions like WiFi.

* WorkManager is the recommended Jetpack solution for guaranteed
  background work. It works on all API levels 14+.

* "Background work" here means deferrable work that should run
  outside the app's foreground lifecycle. It is not the same as
  "work off the main thread" — that is what Dispatchers.IO is for.
```

---

## Common Misconception

A common misconception is:

```text
"If I Use Dispatchers.IO, My Work Survives App Close."
```

Incorrect.

```text
Dispatchers.IO

↓

Changes which thread the coroutine runs on.
Does NOT change its lifecycle.

↓

Still tied to viewModelScope.
Still dies when ViewModel is cleared.
Still dies when process is killed.
```

`Dispatchers.IO` solves the "don't block the main thread" problem.

It does not solve the "work must survive app close" problem.

These are two different problems.

---

## A Natural Question

We know why coroutines are not enough.

We know we need something that survives app close,
can wait for device conditions,
and persists across reboots.

A natural question appears:

```text
What Is This System,
And How Does It Actually Work?
```

That question leads us to:

```text
WorkManager
```

---

## Revision

### Core Idea

```text
Coroutines live inside the app's process.
When the process dies, coroutines die.

Work that must survive app close
needs a system that lives outside the process.
```

### The Boundary

```text
viewModelScope

↓

Tied to ViewModel. Tied to process.
Correct for: user-triggered work, UI work, network/database calls
Wrong for: guaranteed completion, conditional scheduling


WorkManager

↓

Managed by the OS.
Correct for: work that must run eventually
             work that needs device conditions (WiFi, charging)
             work that must survive reboots
```

### What Changes

```text
Before: Work is tied to the ViewModel's lifetime
After:  Work is registered with the OS and runs on the OS's terms
```

### Previous Concept

```text
(DOC 14 — Dependency Injection)
```

### Next Concept

```text
WorkManager
```
