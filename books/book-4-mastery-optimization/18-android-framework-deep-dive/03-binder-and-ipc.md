# Binder and IPC

## Looking Back

In the previous concept, we saw the app process start:

```text
Zygote forks
    ↓
Application.onCreate()
    ↓
MainActivity.onCreate()
    ↓
UI is visible
```

Then we noted something strange:

```text
Your app lives in one process.
system_server lives in another process.

When you call startActivity(), who actually starts it?
The answer has to cross a process boundary.
```

Android runs multiple apps, system services, and your app
all as separate isolated processes.

How do they talk to each other?

---

## The Problem

This question appears when debugging real production issues:

```text
"I called startActivity(intent). My activity started. But...
 how did the Intent get from my process to the system
 that actually started the new Activity?"

"I'm getting TransactionTooLargeException.
 What transaction? What's the limit? Why?"

"My Retrofit call hangs on the main thread and causes an ANR.
 But Retrofit uses OkHttp threads. How does the network
 call block my UI?"

"I called bindService(). It's asynchronous.
 Why can't I get the result immediately?"
```

All of these questions have the same underlying answer: Binder.

---

## The Mental Model

```text
Normal inter-process communication (Linux pipes, sockets)
    ↓
Complex, requires explicit serialization
High overhead for frequent calls

Binder (Android's custom IPC mechanism)
    ↓
Built into the Linux kernel as a character device (/dev/binder)
Optimized for Android's use case
Single-copy data transfer (much faster than standard IPC)
```

**Binder = Android's telephone exchange.**

Every call between processes —
`startActivity`, `bindService`, `getSystemService`,
even the network stack (through the kernel) —
passes through Binder.

It is invisible until something goes wrong.

Then it explains everything.

---

## How a System Call Crosses a Process Boundary

When you call `startActivity(intent)`:

```text
Your app (process A)
    ↓
Calls ActivityManagerService.startActivity()
    ↓
But ActivityManagerService lives in system_server (process B)
    ↓
Your call cannot directly invoke code in another process
    ↓
Binder IPC:
  Your call is marshalled (serialized) into a Binder transaction
  Sent to system_server through /dev/binder (kernel driver)
  Unmarshalled (deserialized) in system_server
  ActivityManagerService executes the call
  Result is sent back through Binder
    ↓
Your process receives the result
Activity starts
```

---

## ASCII Diagram

```
Your App Process                         system_server Process
    │                                           │
    │  startActivity(intent)                    │
    │                                           │
    │  ─── Binder transaction ────────────────▶ │
    │       [intent marshalled]                 │  ActivityManagerService
    │                                           │  validates intent
    │                                           │  finds target Activity
    │                                           │  starts it
    │  ◀─── Binder response ────────────────── │
    │                                           │
    │  Activity.onCreate() is called            │
    │  (in your process)                        │

                    ┌──────────────┐
                    │  /dev/binder │  ← kernel driver
                    │  (shared     │    both processes
                    │   buffer)    │    pass through here
                    └──────────────┘
```

The kernel driver performs the data copy.

This is why Binder is fast:
one copy, not two (normal pipes require a copy into kernel,
then a copy out to the receiving process).

---

## Where Binder Appears in Production Code

Most Binder calls are invisible — they happen inside Android APIs:

```text
startActivity(intent)
    ↓ Binder call to ActivityManagerService

WorkManager.enqueue()
    ↓ Binder call to JobScheduler (system service)

Context.getSystemService(LocationManager::class.java)
    ↓ Returns a proxy — actual calls go through Binder

Room database
    ↓ SQLite runs in your process, but WAL file locks via kernel

Retrofit network call
    ↓ Socket → kernel → network stack (effectively Binder-adjacent)

sendBroadcast(intent)
    ↓ Binder call to ActivityManagerService
    → ActivityManagerService delivers to receivers
```

---

## The TransactionTooLargeException

The most common Binder error in production:

```text
android.os.TransactionTooLargeException
    ↓
Binder transactions have a maximum size: ~1MB per transaction
(actually: 1MB shared across ALL concurrent Binder transactions
 from your process — the effective limit is lower)

Intents cross Binder.
Intent extras must be Parcelable — they are serialized.
Large extras → large transaction → exception.
```

---

## Minimal Code

```kotlin
// WRONG: passing large data through Intent
val intent = Intent(this, DetailActivity::class.java)
intent.putExtra("large_list", ArrayList(thousandsOfItems))
startActivity(intent)  // TransactionTooLargeException
```

```text
The list is serialized into a Binder transaction.
Large list → transaction exceeds ~1MB limit.
Exception thrown — the Activity never starts.
```

---

## Production Code

The correct pattern — pass an ID, load data in the destination:

```kotlin
// WRONG — passes large data through Binder (Intent → startActivity)
val intent = Intent(this, DetailActivity::class.java)
intent.putExtra("order_list", ArrayList(thousandsOfOrders))
startActivity(intent)  // TransactionTooLargeException at scale

// RIGHT — pass a small identifier across Binder
val intent = Intent(this, DetailActivity::class.java)
intent.putExtra("order_id", "abc-123")  // safe: small string
startActivity(intent)

// The destination loads data from Repository — never from Intent
@HiltViewModel
class DetailViewModel @Inject constructor(
    private val orderRepository: OrderRepository,
    savedStateHandle: SavedStateHandle     // receives Intent extras safely
) : ViewModel() {

    // Read the ID from SavedStateHandle (wraps Intent extras)
    private val orderId: String =
        savedStateHandle.get<String>("order_id")
            ?: error("order_id is required")

    // Load from Repository (DOC 12, DOC 14) — not from the Intent
    val order: StateFlow<OrderUiState> = orderRepository
        .observeOrder(orderId)
        .map { OrderUiState.Success(it) }
        .stateIn(viewModelScope, SharingStarted.WhileSubscribed(5_000),
            OrderUiState.Loading)
}
```

### How To Read It

```text
intent.putExtra("order_id", "abc-123")
    ↓
A short string: safe across Binder
The entire Binder transaction for startActivity() stays well under 1MB
Pattern: always pass identifiers, never objects

savedStateHandle.get<String>("order_id")
    ↓
SavedStateHandle wraps the Intent extras
Also survives process death (unlike ViewModel itself)
The canonical way to receive navigation arguments in ViewModels (DOC 5)

orderRepository.observeOrder(orderId)
    ↓
Data loaded from local database / network (DOC 12, DOC 14)
Completely decoupled from how the screen was launched
The ViewModel doesn't know or care how it got the ID
```

---

## Why ANRs Relate to Binder

A common source of ANRs (Application Not Responding):

```text
Main thread calls a Binder-backed API
    ↓
The Binder call is synchronous
    ↓
The system service on the other end is slow or busy
    ↓
Main thread is blocked waiting for the Binder response
    ↓
5 seconds without a response → ANR dialog

Examples:
    PackageManager.queryIntentActivities() — slow on old devices
    ContentResolver.query() — if the ContentProvider is slow
    getSystemService() calls that block — rare but possible
```

This is why:
- `bindService()` is asynchronous — the connection result is a callback
- Network calls on Retrofit use background threads
- Room operations use `Dispatchers.IO` (DOC 2)

---

## Production Notes

- Every `startActivity()`, `sendBroadcast()`, `bindService()` is a Binder call
  through `ActivityManagerService` in `system_server`

- `TransactionTooLargeException` (max ~1MB per transaction) —
  never pass large objects through Intents;
  use SavedStateHandle + Repository instead

- Binder calls on the main thread can cause ANRs if the remote service is slow —
  use asynchronous APIs (`bindService()` callback, `ContentResolver` on IO threads)

- Binder maintains a thread pool on each process for incoming calls —
  AIDL service methods may execute on any Binder thread, not the main thread

- The 1MB limit is shared across all concurrent Binder transactions
  from your process — under load, the effective limit is lower

---

## Common Misconception

A common misconception is:

```text
"My code runs entirely in one process.
 I'm not doing IPC anywhere."
```

Incorrect.

Every interaction with a system service crosses a process boundary via Binder:

```text
startActivity()       ──▶ ActivityManagerService (system_server)
WorkManager.enqueue() ──▶ JobScheduler (system_server)
LocationManager       ──▶ LocationManagerService (system_server)
PackageManager        ──▶ PackageManagerService (system_server)
sendBroadcast()       ──▶ ActivityManagerService (system_server)
```

Your app makes dozens of Binder calls per minute
without you writing a single line of IPC code.

---

## A Natural Question

We've seen how your app talks to the system.

Now a different question:

```text
The user starts music playback.
They press Home.
The app is backgrounded.

Music should keep playing.

WorkManager (DOC 15) can be deferred by the OS.
That's not acceptable for music.

What Android component is designed for
"run this NOW and keep running while the user expects it"?
```

That leads to Foreground Services.

---

## Revision

### Core Idea

```text
Binder
    ↓
Android's kernel-level IPC mechanism
Every cross-process call goes through it
Invisible until something goes wrong
```

### The Call Path

```text
startActivity(intent)
    ↓
Your process → Binder → system_server
    ↓
ActivityManagerService validates and starts Activity
    ↓
Binder → your process
    ↓
Activity.onCreate() called
```

### The 1MB Rule

```text
Intent extras → cross Binder → serialized into transaction
    ↓
Max transaction size: ~1MB (shared across concurrent transactions)
    ↓
Pattern: pass identifiers (String, Long), not objects
    ↓
Load objects in the destination from Repository
```

### What Crosses Binder

```text
startActivity()       → system_server
WorkManager.enqueue() → system_server
getSystemService()    → system_server (returns proxy)
bindService()         → system_server → other process
ContentResolver       → ContentProvider process
```

### Previous Concept

```text
App Process and Zygote
(where the process comes from, what Application.onCreate() does)
```

### Next Concept

```text
Foreground Services
(background work that cannot be deferred)
```
