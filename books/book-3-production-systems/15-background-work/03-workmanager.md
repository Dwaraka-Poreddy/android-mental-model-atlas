# WorkManager

## Looking Back

In the previous concept we saw why coroutines alone fail:

```text
viewModelScope coroutine
        ↓
Lives inside the app's process
        ↓
Process is killed / app is closed
        ↓
Work stops
```

We need a system that survives process death.

A system managed by the OS, not by the ViewModel.

---

## What We Need

```text
Schedule work to run even when the app is closed.
Run the work when device conditions are met.
Guarantee the work runs — even after a device reboot.
```

That system is WorkManager.

---

## The Mental Model

```text
WorkManager

↓

A job scheduler that survives app close,
device reboots, and system process kills.

You describe WHAT to do.
You describe WHEN to run it.
The OS decides the exact moment.
```

WorkManager is not a coroutine scope.

It is not tied to a ViewModel.

It is a persistent queue managed by Android itself.

---

## Coroutines vs WorkManager

```text
Coroutines (DOC 2)              WorkManager
        ↓                             ↓
"Do this now"               "Do this eventually"
"In the app's process"      "Guaranteed, even after reboot"
"Cancel when app closes"    "Persist across process death"
"Result available to UI"    "Result stored, observable later"
```

Neither replaces the other.

Coroutines handle in-app work.

WorkManager handles work that outlives the app.

---

## What WorkManager Guarantees

```text
1. Work will run at least once.

2. Work survives app close, device reboot, and process death.

3. Work can be constrained:
   only on WiFi, only when charging,
   only when the device is idle.

4. Work state is observable from the UI.
   The ViewModel can watch whether work is running,
   succeeded, or failed.
```

These guarantees are backed by persistence.

WorkManager stores every scheduled work request in its own database.

If the device restarts, WorkManager reads that database and reschedules.

---

## How Persistence Works

```text
App schedules work
        ↓
WorkManager stores the request
in its own internal Room database
        ↓
Device restarts / app is killed
        ↓
WorkManager reads its database on next boot
        ↓
Reschedules the work
        ↓
Work runs when constraints are met
```

The work request is not stored in memory.

It is written to disk.

Process death does not erase it.

---

## Minimal Code

```kotlin
// 1. Define what to do
class SyncWorker(context: Context, params: WorkerParameters) : Worker(context, params) {
    override fun doWork(): Result {
        // sync logic here
        return Result.success()
    }
}

// 2. Schedule it
WorkManager.getInstance(context).enqueue(
    OneTimeWorkRequestBuilder<SyncWorker>().build()
)
```

Two steps.

Define the work. Schedule the work.

WorkManager handles everything else.

---

## The Result Type

A Worker returns one of three results:

```text
Result.success()

↓

Work completed. Remove from queue.


Result.failure()

↓

Work failed permanently. Do not retry.


Result.retry()

↓

Work failed temporarily. Retry later.
WorkManager applies exponential backoff.
```

Your `doWork()` function controls the outcome.

WorkManager acts on the result.

---

## Production Code

A worker that syncs orders and handles failure gracefully:

```kotlin
class SyncOrdersWorker(
    context: Context,
    params: WorkerParameters
) : Worker(context, params) {

    override fun doWork(): Result {
        return try {
            val api = OrderApiClient.create()           // network call
            val orders = api.fetchOrders().execute()    // blocking call

            if (orders.isSuccessful) {
                val db = AppDatabase.getInstance(applicationContext)
                db.orderDao().insertAll(orders.body()!!)
                Result.success()
            } else {
                // Server error — retry up to 3 times
                if (runAttemptCount < 3) Result.retry()
                else Result.failure()
            }
        } catch (e: Exception) {
            // Network error — retry
            if (runAttemptCount < 3) Result.retry()
            else Result.failure()
        }
    }

}

// Schedule
val syncRequest = OneTimeWorkRequestBuilder<SyncOrdersWorker>().build()
WorkManager.getInstance(context).enqueue(syncRequest)
```

### How To Read It

```text
Worker(context, params) : Worker(...)

↓

Extend Worker for synchronous (blocking) work.
doWork() must not be a suspend function in the base Worker class.


override fun doWork(): Result

↓

WorkManager calls this on a background thread.
Block freely. Do not call runBlocking from here.


runAttemptCount

↓

WorkManager tracks how many times this work has been attempted.
Starts at 0 on the first attempt.
Increments after each Result.retry().


Result.retry()

↓

WorkManager will reschedule this work with exponential backoff.
Default: 30 seconds, then 60, then 120...


WorkManager.getInstance(context).enqueue(...)

↓

Registers the work request in WorkManager's database.
Returns immediately — work runs later.
```

---

## How WorkManager Decides When To Run

WorkManager is not a real-time scheduler.

It works with the Android platform's job scheduling system.

```text
API 23+     → Uses JobScheduler
Below 23    → Uses AlarmManager + BroadcastReceiver
```

Both are managed by Android, not by your app.

This means:
- WorkManager respects Doze mode
- WorkManager respects battery optimization
- WorkManager may batch work with other apps' work
- Timing is approximate, not exact

---

## Observing Work State

WorkManager exposes a `LiveData` and `Flow` of `WorkInfo`.

```kotlin
WorkManager.getInstance(context)
    .getWorkInfoByIdLiveData(syncRequest.id)
    .observe(lifecycleOwner) { workInfo ->
        when (workInfo?.state) {
            WorkInfo.State.RUNNING   -> showLoading()
            WorkInfo.State.SUCCEEDED -> showSuccess()
            WorkInfo.State.FAILED    -> showError()
            else -> {}
        }
    }
```

The UI can watch the work without being involved in running it.

---

> **You'll see this in...**
> - **DOC 15 (next concept) — CoroutineWorker**, where Worker is upgraded to support suspend functions, connecting WorkManager to your existing coroutine-based data layer
> - **DOC 16 — Testing**, where `TestListenableWorkerBuilder` runs Workers synchronously in tests without a real scheduler

---

## Production Notes

```text
* WorkManager uses JobScheduler internally (API 23+). It abstracts
  over the platform's job scheduling system so you do not need to
  interact with JobScheduler directly.

* Work is persisted in WorkManager's own internal Room database.
  It survives process death, device restarts, and force-stops
  (with some caveats on force-stop — see Production Note below).

* WorkManager is NOT for: precise timing, foreground tasks, or
  work that needs immediate execution. Minimum delay is not
  guaranteed — use it when "eventually" is acceptable.

* For periodic work, the minimum interval is 15 minutes. The OS
  will not run periodic work more frequently than this regardless
  of what you set.

* Force-stopping an app (Settings → App Info → Force Stop) removes
  all WorkManager requests. This is expected OS behavior and cannot
  be overridden.
```

---

## Common Misconception

A common misconception is:

```text
"WorkManager Runs Work Immediately When I Call enqueue()."
```

Incorrect.

```text
WorkManager.enqueue(request)

↓

Stores the request in its database.
Returns immediately.
The work has not started yet.

↓

WorkManager negotiates with JobScheduler.
The OS decides when to run the work
based on battery, Doze mode, and constraints.

↓

Work starts — possibly seconds or minutes later.
```

`enqueue()` is "register this work to run."

It is not "run this work right now."

---

## A Natural Question

`Worker.doWork()` is synchronous.

But our data layer uses coroutines everywhere.

Our Repository has `suspend fun syncOrders()`.

Our DAO returns `Flow<List<OrderEntity>>`.

`doWork()` is a blocking function.

How do coroutines and WorkManager work together?

A natural question appears:

```text
How Do I Call A Suspend Function Inside A Worker?
```

That question leads us to:

```text
CoroutineWorker
```

---

## Revision

### Core Idea

```text
WorkManager

↓

A job scheduler managed by the OS.
Persists work across process death and device reboots.
Runs work when constraints are met.
```

### The Guarantees

```text
Work will run at least once.
Work survives app close and reboots.
Work can be constrained (WiFi, charging, idle).
Work state is observable.
```

### Production Recognition

```kotlin
class SyncWorker(...) : Worker(...) {
    override fun doWork(): Result {
        // blocking work here
        return Result.success()
    }
}

WorkManager.getInstance(context).enqueue(
    OneTimeWorkRequestBuilder<SyncWorker>().build()
)
```

```text
Worker = what to do
OneTimeWorkRequest = schedule once
enqueue = register with the OS
```

### Previous Concept

```text
Why Background Work Exists
```

### Next Concept

```text
CoroutineWorker
```
