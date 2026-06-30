# Constraints

## Looking Back

In the previous concepts we learned:

```text
CoroutineWorker

↓

Defines what to do.

WorkManager.enqueue(request)

↓

Schedules it to run.
```

WorkManager guarantees the work will run.

But sometimes work should only run under certain conditions.

---

## The Problem

An app uploads photos in the background.

Uploading on mobile data is expensive for the user.

The user wants photos to upload only on WiFi.

A `viewModelScope` coroutine cannot wait for WiFi —
it runs immediately or not at all.

Even if we schedule the work with basic WorkManager:

```kotlin
WorkManager.getInstance(context).enqueue(
    OneTimeWorkRequestBuilder<UploadPhotosWorker>().build()
)
```

WorkManager runs the work as soon as it can.

It does not know we only want WiFi.

---

## The Mental Model

```text
Constraints

↓

Conditions that must be true
before WorkManager runs the work.

WorkManager watches for these conditions.
When they are met, the work starts.
```

You describe the requirements.

The OS monitors the device.

When the conditions are met, WorkManager starts the work.

---

## The Available Constraints

```text
NetworkType.CONNECTED        Any network available
NetworkType.UNMETERED        WiFi only (unmetered connection)
NetworkType.METERED          Mobile data
NetworkType.NOT_REQUIRED     No network needed

requiresCharging(true)       Device must be charging

requiresDeviceIdle(true)     Device must be in idle/Doze mode

requiresBatteryNotLow(true)  Battery not critically low

requiresStorageNotLow(true)  Storage not critically low
```

Constraints can be combined.

All constraints must be satisfied simultaneously.

---

## Minimal Code

```kotlin
val constraints = Constraints.Builder()
    .setRequiredNetworkType(NetworkType.UNMETERED)   // WiFi only
    .setRequiresCharging(true)                        // only when charging
    .build()

val uploadRequest = OneTimeWorkRequestBuilder<UploadPhotosWorker>()
    .setConstraints(constraints)
    .build()

WorkManager.getInstance(context).enqueue(uploadRequest)
```

The work is registered.

WorkManager waits.

When the device is on WiFi and charging,
WorkManager starts `UploadPhotosWorker`.

---

## The Flow

```text
WorkManager.enqueue(uploadRequest with WiFi constraint)
        ↓
WorkManager stores the request in its database (survives reboot)
        ↓
System monitors network state
        ↓
WiFi connects
        ↓
WorkManager starts UploadPhotosWorker
        ↓
Work runs and completes → state = SUCCESS
```

If the device reboots before WiFi is available,
WorkManager re-reads its database on boot and resumes waiting.

---

## Production Code

A periodic sync that runs every 15 minutes on any network
when the battery is not low:

```kotlin
val syncConstraints = Constraints.Builder()
    .setRequiredNetworkType(NetworkType.CONNECTED)
    .setRequiresBatteryNotLow(true)
    .build()

val periodicSync = PeriodicWorkRequestBuilder<SyncOrdersWorker>(
    repeatInterval = 15,
    repeatIntervalTimeUnit = TimeUnit.MINUTES
)
    .setConstraints(syncConstraints)
    .setBackoffCriteria(
        BackoffPolicy.EXPONENTIAL,
        WorkRequest.MIN_BACKOFF_MILLIS,
        TimeUnit.MILLISECONDS
    )
    .build()

WorkManager.getInstance(context).enqueueUniquePeriodicWork(
    "sync_orders",
    ExistingPeriodicWorkPolicy.KEEP,
    periodicSync
)
```

### How To Read It

```text
PeriodicWorkRequestBuilder<SyncOrdersWorker>(15, MINUTES)

↓

Run SyncOrdersWorker every 15 minutes.
15 minutes is the minimum interval the OS allows.


setConstraints(syncConstraints)

↓

Only run if: connected to any network AND battery not low.
If constraints fail mid-cycle, the next attempt checks again.


setBackoffCriteria(EXPONENTIAL, MIN_BACKOFF_MILLIS, ...)

↓

If work returns Result.retry():
Wait MIN_BACKOFF_MILLIS, then double the wait time each retry.
10s → 20s → 40s → 80s...
Prevents hammering a failing server.


enqueueUniquePeriodicWork("sync_orders", KEEP, periodicSync)

↓

Only one instance of "sync_orders" can exist in the queue.
"sync_orders" is the unique name — a stable string identifier.

KEEP: if a "sync_orders" request already exists, keep it.
Do not reset its timer. Do not create a duplicate.
```

---

## KEEP vs REPLACE

When calling `enqueueUniquePeriodicWork`,
you must choose a policy for what happens if the work already exists:

```text
ExistingPeriodicWorkPolicy.KEEP

↓

An existing "sync_orders" is already scheduled.
Keep it. Do not restart. Do not reset the timer.
Use this when the schedule must not be disrupted.


ExistingPeriodicWorkPolicy.REPLACE

↓

An existing "sync_orders" is already scheduled.
Cancel it. Schedule the new one.
Use this when the constraints or parameters have changed
and you need a fresh start.
```

In practice, use `KEEP` for periodic background syncs.

Use `REPLACE` when something meaningful has changed —
a new user logged in, for example.

---

## Constraints Are Checked At Start Time

An important behavior to know:

```text
WorkManager checks constraints before starting work.

If constraints pass → work starts.

If WiFi drops while work is running → work continues.

WorkManager does not cancel running work
when constraints are no longer met.
```

Constraints are a gate, not an ongoing check.

Once the gate opens and work starts, it runs to completion.

---

> **You'll see this in...**
> - **DOC 16 — Testing**, where `TestListenableWorkerBuilder` lets you run a ConstrainedWorker in tests without real WiFi or charging state
> - **DOC 17 — Performance**, where battery-aware constraints prevent background work from appearing in battery usage reports

---

## Production Notes

```text
* Periodic work minimum is 15 minutes. The OS will not run it more
  frequently than this, regardless of what you set. Values below
  15 minutes are silently rounded up.

* enqueueUniquePeriodicWork with a stable name prevents duplicate
  workers from accumulating. Always use a stable name for periodic work.

* KEEP vs REPLACE: use KEEP for recurring syncs (don't reset the timer).
  Use REPLACE when the work's parameters have meaningfully changed.

* Constraints are evaluated when the work is about to start.
  If WiFi drops while the work is running, the work is not cancelled.

* NetworkType.UNMETERED means WiFi on most devices.
  It technically means any unmetered connection,
  but in practice WiFi is the common case.
```

---

## Common Misconception

A common misconception is:

```text
"WorkManager Starts Work The Moment Constraints Are Met."
```

Not always.

```text
WiFi connects
        ↓
Constraints are now met
        ↓
WorkManager is notified
        ↓
But the OS may batch this with other work
        ↓
Actual start may be delayed by minutes
under Doze mode or battery optimization
```

Constraints are a minimum requirement, not a trigger.

WorkManager will not run the work before constraints are met.

It may run the work some time after constraints are met.

Use WorkManager when "eventually, when conditions are right"
is acceptable — not when you need real-time responsiveness.

---

## A Natural Question

We have covered:
- Why background work exists
- WorkManager — the scheduler
- CoroutineWorker — how to use suspend functions
- Constraints — how to set conditions

WorkManager can do more.

Multiple workers can be chained:
step one downloads data,
step two processes it,
step three uploads the result.

A natural question appears:

```text
How Do Multiple Workers Run In Sequence?
```

That question leads us to:

```text
Work Chaining
```

---

## Revision

### Core Idea

```text
Constraints

↓

Conditions that must be true before WorkManager runs the work.
WorkManager monitors device state and starts work when ready.
```

### The Pattern

```kotlin
val constraints = Constraints.Builder()
    .setRequiredNetworkType(NetworkType.UNMETERED)
    .setRequiresBatteryNotLow(true)
    .build()

val request = PeriodicWorkRequestBuilder<SyncWorker>(15, TimeUnit.MINUTES)
    .setConstraints(constraints)
    .build()

WorkManager.getInstance(context)
    .enqueueUniquePeriodicWork("sync", ExistingPeriodicWorkPolicy.KEEP, request)
```

```text
Constraints.Builder()           define conditions
setRequiredNetworkType(...)     network condition
setRequiresBatteryNotLow(true)  battery condition
enqueueUniquePeriodicWork(...)  one instance, stable name
KEEP                            don't reset the existing timer
```

### The Complete Picture

```text
CoroutineWorker       defines what to do
Constraints           defines when it is allowed
WorkRequest           combines the two
WorkManager.enqueue   registers with the OS
OS                    monitors conditions and starts work
```

### Previous Concept

```text
CoroutineWorker
```

### Next Concept

```text
Work Chaining
```
