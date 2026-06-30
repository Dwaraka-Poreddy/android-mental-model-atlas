# CoroutineWorker

## Looking Back

In the previous concept we learned that WorkManager's `Worker` class
runs synchronous, blocking code:

```kotlin
class SyncWorker(context: Context, params: WorkerParameters) : Worker(context, params) {
    override fun doWork(): Result {
        // blocking code only
        return Result.success()
    }
}
```

We know our data layer uses suspend functions (from DOC 2):

```kotlin
// OrderRepository — built in DOC 12
suspend fun syncOrders(): List<Order>

// OrderDao — built in DOC 12
fun observeOrders(): Flow<List<OrderEntity>>
```

A natural question appears:

```text
doWork() Is Blocking.
Our Repository Is Coroutine-Based.
How Do They Work Together?
```

---

## The Problem

If we try to call a suspend function inside a plain `Worker`:

```kotlin
class SyncWorker(...) : Worker(...) {
    override fun doWork(): Result {
        // ERROR: Suspend function cannot be called here.
        // doWork() is not a suspend function.
        orderRepository.syncOrders()
        return Result.success()
    }
}
```

`doWork()` is a regular function.

Suspend functions can only be called from other suspend functions
or from a coroutine.

We cannot call our Repository from here.

---

## The Workaround (And Why We Don't Use It)

We could launch a coroutine manually inside `doWork()`:

```kotlin
override fun doWork(): Result {
    runBlocking {
        orderRepository.syncOrders()
    }
    return Result.success()
}
```

`runBlocking` bridges the gap — it blocks the current thread
until the coroutine completes.

This works, but it has problems:

```text
runBlocking

↓

Blocks the WorkManager thread pool thread.
Thread is held hostage until the coroutine finishes.
If the work is cancelled, the coroutine doesn't know.
No structured cancellation.
```

There is a better way.

---

## CoroutineWorker

WorkManager provides `CoroutineWorker` specifically for coroutine-based work.

```text
CoroutineWorker

↓

Worker with a built-in coroutine scope.

doWork() becomes suspend fun doWork().

You can call any suspend function inside it.
The scope is cancelled when WorkManager cancels the work.
```

---

## The Difference

```kotlin
// Worker — synchronous, blocking
class SyncWorker(
    context: Context,
    params: WorkerParameters
) : Worker(context, params) {

    override fun doWork(): Result {
        // Must use blocking calls only
        return Result.success()
    }

}


// CoroutineWorker — coroutine-native
class SyncWorker(
    context: Context,
    params: WorkerParameters
) : CoroutineWorker(context, params) {

    override suspend fun doWork(): Result {
        // Can call suspend functions
        // Has a built-in coroutine scope
        return Result.success()
    }

}
```

One word changes — `Worker` becomes `CoroutineWorker`.

`doWork()` becomes `suspend fun doWork()`.

Everything in the coroutine world is now available.

---

## Minimal Code

```kotlin
class SyncOrdersWorker(
    context: Context,
    params: WorkerParameters
) : CoroutineWorker(context, params) {

    override suspend fun doWork(): Result {
        orderRepository.syncOrders()   // suspend fun — works directly
        return Result.success()
    }

}
```

Three lines.

No `runBlocking`. No thread management. No callback.

---

## Connecting To Hilt

Our Workers often need dependencies —
the Repository, the DAO, the API client.

Hilt (DOC 14) can inject into Workers using `@HiltWorker`:

```kotlin
@HiltWorker
class SyncOrdersWorker @AssistedInject constructor(
    @Assisted context: Context,
    @Assisted params: WorkerParameters,
    private val orderRepository: OrderRepository
) : CoroutineWorker(context, params) {

    override suspend fun doWork(): Result {
        return try {
            orderRepository.syncOrders()
            Result.success()
        } catch (e: Exception) {
            if (runAttemptCount < 3) Result.retry()
            else Result.failure()
        }
    }

}
```

### How To Read It

```text
@HiltWorker + @AssistedInject constructor

↓

Hilt (DOC 14) injects OrderRepository into the worker.
@Assisted marks the parameters WorkManager provides (context, params).
@AssistedInject is required when some parameters come from Hilt
and others come from the caller (WorkManager).


override suspend fun doWork()

↓

CoroutineWorker provides a coroutine scope (Dispatchers.Default by default).
We can call suspend functions directly.
No runBlocking. No thread blocking.


orderRepository.syncOrders()

↓

The same Repository from DOC 12.
WorkManager and the data layer connect here.
The data layer does not know it is being called from a Worker.


runAttemptCount

↓

WorkManager tracks how many times this work has been attempted.
Retry 3 times before giving up permanently.
```

---

## Cancellation Is Automatic

When WorkManager cancels a work request
(because constraints are no longer met, or the user cancelled it),
the CoroutineWorker's scope is cancelled.

```text
WorkManager cancels work
        ↓
CoroutineWorker's coroutine scope is cancelled
        ↓
Any running suspend function receives CancellationException
        ↓
doWork() exits cleanly
        ↓
Structured cancellation — the same pattern as DOC 2
```

This is why `CoroutineWorker` is always preferred over plain `Worker`.

---

## Wiring Hilt With WorkManager

Hilt needs one extra setup step to know how to inject Workers:

```kotlin
// In your Application class
@HiltAndroidApp
class MyApplication : Application(), Configuration.Provider {

    @Inject
    lateinit var workerFactory: HiltWorkerFactory

    override val workManagerConfiguration: Configuration
        get() = Configuration.Builder()
            .setWorkerFactory(workerFactory)
            .build()

}
```

And in `AndroidManifest.xml`, disable the default WorkManager initializer:

```xml
<provider
    android:name="androidx.startup.InitializationProvider"
    android:authorities="${applicationId}.androidx-startup"
    tools:node="remove" />
```

This tells WorkManager to use Hilt's factory instead of its default one.

---

> **You'll see this in...**
> - **DOC 14 — Dependency Injection**, where `@HiltWorker` + `@AssistedInject` is the same Hilt pattern applied to Workers instead of ViewModels
> - **DOC 16 — Testing**, where `TestListenableWorkerBuilder` constructs a CoroutineWorker with a fake repository injected directly, no scheduler needed

---

## Production Notes

```text
* @HiltWorker + @AssistedInject is the standard pattern for Workers
  that need injected dependencies. Without this, you would need a
  custom WorkerFactory.

* CoroutineWorker runs on Dispatchers.Default by default. You can
  override withContext(Dispatchers.IO) inside doWork() if the work
  is I/O bound.

* CoroutineWorker's scope is cancelled automatically when WorkManager
  cancels the work. You do not need to manage this manually.

* Result.retry() triggers an automatic retry with exponential
  backoff. The delay grows: 30s → 60s → 120s → 240s...

* CoroutineWorker is always the right choice when your app uses
  coroutines — which is always.
```

---

## Common Misconception

A common misconception is:

```text
"CoroutineWorker Runs On The Main Thread,
 Because Coroutines Run On The Main Thread."
```

Incorrect.

```text
CoroutineWorker

↓

Default dispatcher: Dispatchers.Default
(not Dispatchers.Main)

↓

Runs on a background thread pool.
Safe for CPU work.
Use withContext(Dispatchers.IO) for I/O inside doWork().
```

Coroutines run wherever their dispatcher sends them.

`CoroutineWorker` uses `Dispatchers.Default`, not `Dispatchers.Main`.

The UI thread is not involved.

---

## A Natural Question

We know how to define work (Worker/CoroutineWorker).

We know how to schedule it once (`OneTimeWorkRequestBuilder`).

But WorkManager can do more than schedule one-off work.

We may want:
- Upload photos only on WiFi
- Sync every 15 minutes, only when the battery is not low
- Upload only when charging

A natural question appears:

```text
How Do We Tell WorkManager
When The Work Is Allowed To Run?
```

That question leads us to:

```text
Constraints
```

---

## Revision

### Core Idea

```text
CoroutineWorker

↓

Worker with suspend fun doWork().
Built-in coroutine scope.
Can call any suspend function from your existing data layer.
Scope is cancelled when WorkManager cancels the work.
```

### The Pattern

```kotlin
@HiltWorker
class SyncWorker @AssistedInject constructor(
    @Assisted context: Context,
    @Assisted params: WorkerParameters,
    private val repository: OrderRepository   // Hilt injects this
) : CoroutineWorker(context, params) {

    override suspend fun doWork(): Result {
        return try {
            repository.syncOrders()
            Result.success()
        } catch (e: Exception) {
            if (runAttemptCount < 3) Result.retry() else Result.failure()
        }
    }

}
```

```text
@HiltWorker       = Hilt can inject into this Worker
@AssistedInject   = WorkManager provides context + params; Hilt provides the rest
suspend fun       = call suspend functions directly
runAttemptCount   = retry with backoff up to N times
```

### Previous Concept

```text
WorkManager
```

### Next Concept

```text
Constraints
```
