# App Process and Zygote

## Looking Back

By this point we have covered most of the Android mental model:

```text
DOC 2  — Coroutines (dispatchers, structured concurrency)
DOC 5  — Activity, ViewModel, lifecycle
DOC 12 — Room, SQLite
DOC 14 — Hilt, process-level dependency injection
DOC 15 — WorkManager, background work
DOC 17 — Performance, memory, startup
```

All of it — every coroutine, every ViewModel,
every Hilt-injected class —
runs inside something.

A process.

A natural question appears:

```text
"What actually happens between the user tapping
 the icon and my Application.onCreate() being called?"
```

This concept answers that question.

---

## The Problem

Production developers eventually encounter a cluster of questions
that sound unrelated but have the same answer:

```text
"Why is my cold start slow even though my Activity is simple?"

"If I put database initialization in Application.onCreate(),
 does it run every time?"

"My app was killed by the OS. What runs when it comes back?"

"Hilt says it initializes once at the process level.
 What IS a process?"
```

To answer all of these, you need the mental model
of how Android starts your app.

---

## The Mental Model

```text
Android boots
    ↓
Zygote starts (a special pre-warmed process)
    ↓
Zygote loads Android framework classes + ART
    ↓
Zygote waits

User taps app icon
    ↓
System tells Zygote: "Fork for com.example.app"
    ↓
Zygote forks itself (copy-on-write)
    ↓
New process inherits framework classes (already loaded — fast)
    ↓
Application.onCreate() runs
    ↓
MainActivity.onCreate() runs
```

**Zygote is a pre-warmed template process.**

Every app process is a fork of Zygote.

This is why Android can start a new app process in ~50ms
instead of seconds —
the heavy lifting (loading the framework) is already done.

---

## ASCII Diagram

```
Android OS
    │
    ├── Zygote Process (pre-warmed, always running)
    │       │
    │       ├── [fork] ──▶ com.example.app process
    │       │                   │
    │       │                   ├── Application.onCreate()
    │       │                   │       │
    │       │                   │       ├── Hilt init (@HiltAndroidApp)
    │       │                   │       ├── Timber.plant()
    │       │                   │       └── StrictMode setup
    │       │                   │
    │       │                   └── MainActivity.onCreate()
    │       │
    │       └── [fork] ──▶ com.example.other process
    │                           │
    │                           └── Application.onCreate() (separate)
    │
    └── system_server Process
            │
            ├── ActivityManagerService
            ├── PackageManagerService
            ├── WindowManagerService
            └── ... (all system services)
```

Two key observations:

```text
1. Zygote forks — it does NOT start fresh each time
   Framework classes are shared via copy-on-write memory
   This is the source of Android's fast startup

2. system_server is a separate process from your app
   Every startActivity(), getSystemService() crosses a process boundary
   (more on this in Binder — the IPC Backbone)
```

---

## Why This Matters for Production

```text
@HiltAndroidApp initializes Hilt in Application.onCreate()
    ↓
Hilt's dependency graph is built once per process
Cost: proportional to your @Module / @Provides count
Make it lean — this runs on every cold start

Room.databaseBuilder() in Application is process-singleton
    ↓
Built once when the process starts
If Application.onCreate() is slow: your cold start is slow
This is why DOC 17 measures "time to first frame"

LeakCanary installs in Application.onCreate()
    ↓
Starts watching immediately from process start
Debug-only — remove from production builds

Process death (system kills your app for memory)
    ↓
Next launch = new process, new Application.onCreate()
ViewModel.onSaveInstanceState still needed for UI state
Hilt rebuilds its graph from scratch
```

---

## Minimal Code

```kotlin
@HiltAndroidApp
class FoodApp : Application() {

    override fun onCreate() {
        super.onCreate()
        Timber.plant(Timber.DebugTree())
    }

}
```

```text
@HiltAndroidApp
    ↓
Generates the Hilt application component
Runs before the contents of onCreate()
This is why Hilt is available everywhere in the app
```

---

## Production Code

What goes in `Application.onCreate()` — and why order matters:

```kotlin
@HiltAndroidApp
class FoodApp : Application() {

    override fun onCreate() {
        super.onCreate()

        // 1. Logging — first, so all subsequent logs are captured
        if (BuildConfig.DEBUG) {
            Timber.plant(Timber.DebugTree())
        }

        // 2. Crash reporting — early, to capture crashes in init
        // TODO: Securely load this value from an environment variable or secrets vault. Do not hardcode.
        FirebaseCrashlytics.getInstance()
            .setCrashlyticsCollectionEnabled(!BuildConfig.DEBUG)

        // 3. StrictMode — catches main thread I/O in debug builds
        if (BuildConfig.DEBUG) {
            StrictMode.setThreadPolicy(
                StrictMode.ThreadPolicy.Builder()
                    .detectDiskReads()
                    .detectDiskWrites()
                    .detectNetwork()
                    .penaltyLog()
                    .build()
            )
        }

        // Hilt initialization happens via @HiltAndroidApp — not manually
        // Room, Retrofit, etc. are Hilt-provided — not initialized here directly
    }

}
```

### How To Read It

```text
@HiltAndroidApp
    ↓
Hilt generates the Application-level component
Runs before the contents of onCreate()
The annotation IS the initialization — you don't call anything manually

Timber first
    ↓
If crash reporting logs internally, those logs are captured
If something crashes during init, you see the logs
Logging must exist before anything that might log

StrictMode.detectDiskReads() in DEBUG
    ↓
Throws a logged error if main thread does disk I/O
Catches Room/file access bugs before they reach production
Related: why Dispatchers.IO matters (DOC 2)
StrictMode is removed from release builds by the compiler

No database or network initialization here
    ↓
Hilt provides these as singletons via @Provides
They are initialized on first use, not at app start
This keeps Application.onCreate() fast
```

---

## Process Death — The Detail Most Developers Miss

The OS can kill your app process at any time for memory.

This is not a crash. It is not an error.

It is expected platform behavior.

```text
User opens your app.
App process starts.
Application.onCreate() runs.
User presses Home.

Later:
OS is under memory pressure.
OS kills your app process (silently).
No onDestroy() called. No cleanup. No warning.

User opens your app again.
New process starts.
Application.onCreate() runs again.
```

What this means for production:

```text
1. Application.onCreate() IS your app's boot sequence.
   Every cold start runs it in full.
   Keep it under 50ms on the main thread.

2. Process death resets all in-memory state.
   Room database: rebuilt from disk (data is safe, it's SQLite)
   Hilt graph: rebuilt from scratch
   Coroutines: cancelled
   ViewModel: gone — only SavedStateHandle survives

3. onDestroy() is not guaranteed.
   Never rely on Activity or Service onDestroy() for critical cleanup.
   Use WorkManager for deferred work that must survive process death (DOC 15).
```

---

## Production Notes

- `Application.onCreate()` runs on the main thread —
  keep it fast, under 50ms total

- Heavy initialization (database schema migration, large network init)
  belongs in a background thread or in Hilt's lazy initialization

- Process death does NOT call `onDestroy()` on any component —
  do not rely on it for cleanup or data persistence

- Multi-process apps (rare, used for some services and content providers)
  each get their own `Application.onCreate()` —
  Hilt's graph does NOT cross process boundaries

- StrictMode is your best tool for catching hidden main-thread work
  during development — it catches slow startup before users see it

- Cold start time correlates directly with what you put in
  `Application.onCreate()` — profile it with Android Studio's profiler

---

## Common Misconception

A common misconception is:

```text
"Application.onCreate() runs once per user launch."
```

Incorrect.

```text
"Application.onCreate() runs once per PROCESS start."
```

The distinction matters:

```text
User opens app ──▶ process starts ──▶ Application.onCreate()
User presses Home ──▶ process may stay alive
User opens app again ──▶ process is STILL alive
    ──▶ Application.onCreate() does NOT run again

Later, OS kills the process for memory
User opens app again ──▶ NEW process starts
    ──▶ Application.onCreate() runs again
```

The process can survive across many user "launches"
if the OS does not reclaim it.

`Application.onCreate()` only runs again after process death.

---

## A Natural Question

The process is running.

`Application.onCreate()` has finished.

Now you call `startActivity(intent)`.

Your Activity starts.

But Activity lives in your process.

`ActivityManagerService` lives in `system_server` — a different process.

How does your `startActivity()` call reach the system?

How did the Intent cross a process boundary?

```text
The answer is Binder.
Android's inter-process communication backbone.
```

---

## Revision

### Core Idea

```text
Zygote
    ↓
Pre-warmed template process
Loads Android framework once at boot
Forks for each new app process
This is why app startup is fast (~50ms, not seconds)
```

### The Startup Sequence

```text
1. OS boot → Zygote starts
2. Zygote → loads framework classes + ART → waits
3. User taps icon → system tells Zygote to fork
4. Fork → new process inherits loaded framework
5. Application.onCreate() → Hilt graph built
6. MainActivity.onCreate() → UI starts
```

### The Process Death Sequence

```text
1. App in background
2. OS kills process (no notification, no onDestroy)
3. User re-opens app
4. New process → Application.onCreate() again
5. ViewModel gone — SavedStateHandle recovers UI state
6. Room database rebuilt from disk — data is safe
```

### What Belongs in Application.onCreate()

```text
FAST (< 50ms, main thread)       WRONG (causes slow cold start)
─────────────────────────────    ─────────────────────────────────
Timber.plant()                   Heavy network initialization
Crash reporter init              Database migration on main thread
StrictMode setup (debug)         Synchronous file reads
@HiltAndroidApp (annotation)     Network calls at startup
```

### Previous Concept

```text
DOC 17 — Performance & Optimization
(measuring cold start, understanding what to optimize)
```

### Next Concept

```text
Binder — The IPC Backbone
(how components and system services communicate across processes)
```
