# Foreground Services

## Looking Back

In DOC 15 we learned WorkManager for guaranteed background work:

```text
WorkManager (DOC 15)
    ↓
"Run this task. I don't care exactly when."
OS schedules it — might be now, might be later
Survives process death
Respects battery optimization
Perfect for: sync, upload, cleanup
```

WorkManager is the right tool for most background work.

But WorkManager has a constraint:

```text
WorkManager defers to the OS.
The OS can postpone your work for battery optimization.
The OS can batch your work with other apps.
Minimum periodic interval: 15 minutes.
```

What if work **cannot** be deferred?

---

## The Problem

Certain use cases require work to run **right now**,
for as long as the user expects:

```text
User starts a music player
    ↓
Music must keep playing even if the user closes the app
Cannot be deferred — a 2 second gap is audible

User starts navigation
    ↓
GPS tracking must not stop while navigating
Cannot be deferred — missing a turn is serious

User uploads a large file
    ↓
Upload should not be interrupted mid-way
Cannot be deferred — partial upload may fail to resume

Common property:
    ↓
Work is ongoing while the user explicitly expects it to run
The user started it intentionally
The user can see evidence it is running
```

---

## WorkManager vs Foreground Service

```text
WorkManager (DOC 15)                Foreground Service
─────────────────────────────────   ─────────────────────────────────
"Run this eventually"               "Run this NOW, keep running"
OS controls timing                  User controls via notification
Survives process death              Tied to process lifetime
Respects battery optimization       Runs regardless of battery state
Best for: sync, backup, upload      Best for: media, GPS, calls, sync
Min periodic: 15 minutes            Runs as long as needed
No user-visible artifact required   REQUIRES persistent notification
```

---

## The Mental Model

```text
A Foreground Service
    ↓
An Android component that runs continuously
Tied to a PERSISTENT NOTIFICATION that the user can see

The notification is the contract:
"As long as you see this notification, work is running."
"You can stop it by stopping the playback / navigation."
```

This is intentional.

Android requires the notification to prevent apps
from silently running forever in the background,
draining battery and data without the user knowing.

```text
No notification → no foreground service
The system enforces this automatically
Foreground service without notification: ForegroundServiceDidNotStartInTimeException
```

---

## ASCII Diagram

```
User Action
    │
    │  "Start Music"
    ▼
MusicPlayerService.onStartCommand()
    │
    ├── startForeground(id, notification)    ← REQUIRED within 5s
    │       │
    │       └── Persistent Notification appears in status bar
    │               "♪ Now Playing: Song Title"
    │               [Pause] [Skip]
    │
    └── Actual work begins (e.g., ExoPlayer)


User presses Home
    │
    ├── App goes to background
    ├── MusicPlayerService keeps running     ← elevated priority
    └── Notification remains visible        ← user can see and stop it


User dismisses notification (if dismissible)
    │
    └── Service stops (depends on configuration)
```

---

## Minimal Code

```kotlin
class MusicPlayerService : Service() {

    override fun onStartCommand(intent: Intent?, flags: Int, startId: Int): Int {
        val notification = buildNotification()
        startForeground(NOTIFICATION_ID, notification)  // required

        startPlaying()

        return START_STICKY  // restart if killed by OS
    }

    override fun onBind(intent: Intent?): IBinder? = null
}
```

```text
startForeground(NOTIFICATION_ID, notification)
    ↓
Must be called within 5 seconds of onStartCommand
This is what promotes the service to "foreground" status
Without it: the service is background and subject to OS limits

START_STICKY
    ↓
If the OS kills the service for memory, restart it
Intent may be null on restart — handle that case
For music: you want the service to come back
```

---

## Production Code

A coroutine-based foreground service for data sync:

```kotlin
class SyncService : Service() {

    // Service creates its own scope — no viewModelScope here
    private val serviceScope = CoroutineScope(Dispatchers.IO + SupervisorJob())

    override fun onStartCommand(intent: Intent?, flags: Int, startId: Int): Int {

        // Must call within 5 seconds — do it first
        startForeground(
            SYNC_NOTIFICATION_ID,
            buildSyncNotification("Syncing orders...")
        )

        serviceScope.launch {
            try {
                // Inject via Hilt @AndroidEntryPoint on Service
                orderRepository.syncAllPendingOrders()  // DOC 12, DOC 14

                // Work done — stop the service
                stopSelf()
            } catch (e: CancellationException) {
                throw e  // always rethrow cancellation
            } catch (e: Exception) {
                Timber.e(e, "Sync failed")
                stopSelf()
            }
        }

        // Don't restart if killed — WorkManager handles retry logic
        return START_NOT_STICKY
    }

    override fun onDestroy() {
        super.onDestroy()
        serviceScope.cancel()  // cancel all running coroutines
    }

    override fun onBind(intent: Intent?): IBinder? = null

    private fun buildSyncNotification(message: String): Notification {
        return NotificationCompat.Builder(this, SYNC_CHANNEL_ID)
            .setContentTitle("Syncing")
            .setContentText(message)
            .setSmallIcon(R.drawable.ic_sync)
            .setOngoing(true)  // cannot be dismissed by user
            .build()
    }
}
```

### How To Read It

```text
CoroutineScope(Dispatchers.IO + SupervisorJob())
    ↓
Service creates its own coroutine scope
No viewModelScope here — there is no ViewModel
SupervisorJob: if one coroutine fails, others continue (DOC 2)
Dispatchers.IO: sync work happens off the main thread

startForeground(SYNC_NOTIFICATION_ID, notification)
    ↓
Must be called within 5 seconds of onStartCommand
Promotes service to foreground status — elevated OS priority
Android 12+: ForegroundServiceDidNotStartInTimeException if delayed
The notification is required — no silent foreground services

serviceScope.cancel() in onDestroy()
    ↓
Critical: cancels all running coroutines when service stops
Without this: coroutines outlive the service
Memory leak + continued work after the service is "stopped"
Same pattern as viewModelScope.cancel() in ViewModel.onCleared()

stopSelf()
    ↓
The service stops itself when work is complete
Don't leave services running after work is done
Lingering services waste battery and show stale notifications

START_NOT_STICKY
    ↓
If OS kills this service, don't restart it
WorkManager handles retry for sync tasks (DOC 15)
Music services typically use START_STICKY instead
```

---

## Foreground Service Types (Android 10+)

Android 10 introduced a required `foregroundServiceType` attribute
in the manifest:

```xml
<!-- AndroidManifest.xml -->
<service
    android:name=".SyncService"
    android:foregroundServiceType="dataSync" />
```

```text
Available types:
    mediaPlayback    → music, podcast, video
    mediaProjection  → screen recording
    phoneCall        → VOIP calls
    location         → GPS tracking, navigation
    camera           → camera in background
    microphone       → audio recording
    dataSync         → file sync, upload, download
    connectedDevice  → Bluetooth device management

Android 14+: each type requires a specific permission
dataSync → FOREGROUND_SERVICE_DATA_SYNC permission
location → FOREGROUND_SERVICE_LOCATION permission

Google Play enforces appropriate use of foreground service types
Using mediaPlayback for data sync: policy violation risk
```

---

## WorkManager and Foreground Services Together

WorkManager (DOC 15) can start a Foreground Service
for user-visible long-running tasks:

```kotlin
class SyncWorker(
    appContext: Context,
    params: WorkerParameters
) : CoroutineWorker(appContext, params) {

    override suspend fun doWork(): Result {
        // Tell WorkManager this is foreground work
        // WorkManager creates and manages the notification
        setForeground(createForegroundInfo())

        doLongRunningSync()

        return Result.success()
    }

    private fun createForegroundInfo(): ForegroundInfo {
        val notification = buildNotification("Syncing...")
        return ForegroundInfo(NOTIFICATION_ID, notification)
    }
}
```

```text
setForeground(foregroundInfo)
    ↓
Tells WorkManager to promote this Worker to foreground
WorkManager handles the Service lifecycle for you
Use this when: the work is long, user-visible, cannot be deferred
Benefit: WorkManager still handles constraints + retry
```

---

> **You'll see this in...**
> - **DOC 15 — Background Work**, where WorkManager's `setForeground()` lets you run
>   user-visible long-running tasks without managing a Service directly
> - **DOC 17 — Performance & Optimization**, where battery optimization (Doze mode)
>   skips deferred WorkManager work but respects Foreground Services

---

## Production Notes

- Foreground Services require a persistent visible notification —
  this is non-negotiable and enforced by the platform

- Android 14+: you must declare specific permissions for each foreground service type
  (`FOREGROUND_SERVICE_DATA_SYNC`, `FOREGROUND_SERVICE_LOCATION`, etc.)

- Most "background upload" use cases can use WorkManager with `setForeground()` —
  prefer this over managing a Service directly when possible

- The user can force-stop foreground services from system settings —
  your service can be killed at any time; design for it

- For long-running work that the user cannot directly observe,
  WorkManager (DOC 15) is always preferred over a background Service

- `serviceScope.cancel()` in `onDestroy()` is mandatory —
  without it, coroutines outlive the Service and become memory leaks

---

## Common Misconception

A common misconception is:

```text
"Foreground Services prevent the app from being killed."
```

Incorrect.

```text
Foreground Services significantly REDUCE the chance of being killed.
The OS gives foreground services elevated priority.

But in extreme memory pressure:
    ↓
The OS can still kill your process.
The foreground service dies with it.

The service restart policy (START_STICKY) determines what happens:
    START_STICKY    → service restarts, intent may be null
    START_NOT_STICKY → service does not restart
    START_REDELIVER_INTENT → service restarts with original intent
```

Design your foreground service to handle being killed
and restarted at any point.

---

## A Natural Question

Foreground Services, WorkManager, and coroutines
are all mechanisms for running work.

But sometimes work crosses process boundaries —
your app needs to expose data to other apps,
or receive data from the system.

```text
How does one app read contacts, media, or calendar data
that belongs to another app?

How does the system expose structured data
that any app can query?
```

That leads to Content Providers —
Android's mechanism for structured cross-process data sharing.

---

## Revision

### Core Idea

```text
Foreground Service
    ↓
"Work that is ongoing while the user expects it to run"
Requires a persistent visible notification
Cannot be silently deferred by the OS
The notification IS the user's contract with the work
```

### The Lifecycle

```text
startForegroundService(intent) called
    ↓
Service.onStartCommand()
    ↓
startForeground(id, notification) ← must happen within 5s
    ↓
Work begins (coroutines, ExoPlayer, etc.)
    ↓
Work completes → stopSelf()
    ─OR─
User stops it → stopService() from UI
    ↓
Service.onDestroy()
    ↓
serviceScope.cancel() ← cancel all coroutines
```

### Choosing the Right Tool

```text
Work deferred by OS? User doesn't notice?
    → WorkManager (DOC 15)

Work must run now? User sees a notification?
    → Foreground Service
    (or WorkManager + setForeground() for WorkManager-managed work)

Work runs while screen is on? In the composable?
    → viewModelScope + coroutines (DOC 2, DOC 5)
```

### The Notification Contract

```text
User can ALWAYS see that work is running
User can ALWAYS stop it (navigation stop, pause button)
No hidden background processing
This is the Android design contract for long-running work
```

### Previous Concept

```text
Binder and IPC
(how cross-process calls work, TransactionTooLargeException)
```

### Next Concept

```text
Content Providers
(cross-process structured data sharing)
```
