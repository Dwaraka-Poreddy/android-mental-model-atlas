# Offline-First Architecture

## Looking Back

In the previous concept we established:

```text
Single Source of Truth

↓

UI reads from the local database only.
Network writes to that database.
Room emits automatically.
```

This means the UI has data even before the network responds.

A natural question appears:

```text
What If The Network Never Responds?

What If The User Has No Internet?
```

---

## The Common Approach

Most apps handle no-internet like this:

```text
User opens app

↓

App tries to fetch from network

↓

Network fails (no internet)

↓

App shows an error screen

↓

User stares at:
"No internet connection. Please try again."
```

The user has no useful information.

They cannot browse.

They cannot act.

They wait.

---

## The Problem With The Common Approach

In a food delivery app,

the user's order history is on the server.

Their favorite restaurants are on the server.

They have opened the app before.

All that data is already on the device.

But the app shows nothing.

Because the app is waiting for a network response that isn't coming.

```text
The Data Is There.
The App Refuses To Show It.
```

That is a choice.

And it is the wrong choice.

---

## The Mental Model

```text
Offline-First =
  The App Works Correctly With No Internet
  And Improves When Internet Is Available.
```

"Correctly" means:

```text
The user can see data.
The user can interact with cached content.
The user is not blocked.
```

"Improves" means:

```text
When internet returns,
fresh data arrives silently.
The UI updates.
The user notices nothing except better data.
```

---

## The Three States

An offline-first app handles three situations:

```text
State 1: Local data exists. Network succeeds.

↓

Show local data immediately.
Fetch fresh data in background.
Replace local data with fresh data.
User sees a smooth update.

---

State 2: Local data exists. Network fails.

↓

Show local data.
Silently swallow the network error.
Show a subtle "Last updated X minutes ago" indicator.
User is not blocked.

---

State 3: Local data does not exist. Network fails.

↓

Nothing to show.
Show an error or empty state with retry.
(This is the only case where the user is blocked.)
```

State 3 is a first-launch edge case.

States 1 and 2 are the normal operating modes.

---

## ASCII Diagram — The Three Paths

```text
User Opens App
      │
      ▼
  Local DB Has Data?
      │
   ┌──┴──┐
  YES    NO
   │      │
   ▼      ▼
Show     Trigger
Local    Network
Data     Request ──────────────────────────┐
   │                                       │
   └──── Trigger Network Request           │
              │                         Network
           Network                      Fails
           Succeeds                        │
              │                           ▼
              ▼                      Show Error
         Write to DB                (Only path
              │                      that blocks)
              ▼
         Room emits
              │
              ▼
         UI updates
         silently
```

The user is unblocked in two of three paths.

---

## Minimal Code

```kotlin
class OrderRepository(
    private val remoteDataSource: OrderRemoteDataSource,
    private val orderDao: OrderDao
) {

    // Returns local data immediately. Refresh separately.
    fun observeOrders(): Flow<List<Order>> =
        orderDao.observeAll()
            .map { entities -> entities.map { it.toDomain() } }

    // Best-effort refresh. Failures do not crash the observer.
    suspend fun refresh(): Result<Unit> = runCatching {
        val dtos = remoteDataSource.getOrders()
        orderDao.replaceAll(dtos.map { it.toEntity() })
    }

}
```

`observeOrders()` returns local data immediately.

`refresh()` is a best-effort operation.

If it fails, the observer is not affected.

---

## Production Code

A complete offline-first implementation for an order history screen:

```kotlin
class OrderRepository(
    private val remoteDataSource: OrderRemoteDataSource,
    private val orderDao: OrderDao,
    private val clock: Clock = Clock.systemUTC()
) {

    // Always read from local. Immediate. Never blocks on network.
    fun observeOrders(): Flow<List<Order>> =
        orderDao.observeAll()
            .map { entities -> entities.map { it.toDomain() } }

    // Network refresh: best-effort. Returns success or failure.
    // Callers decide what to show on failure.
    suspend fun refreshOrders(): Result<Unit> = runCatching {
        val dtos = remoteDataSource.getOrders()
        val entities = dtos.map { it.toEntity() }
        orderDao.replaceAll(entities)
        orderDao.setLastRefreshed(clock.instant())
    }

    // Exposes when the data was last successfully refreshed.
    // Used to show "Last updated X minutes ago" in the UI.
    fun observeLastRefreshed(): Flow<Instant?> =
        orderDao.observeLastRefreshed()

    // Checks if a background refresh is needed (e.g., data is stale).
    suspend fun isStale(): Boolean {
        val lastRefreshed = orderDao.getLastRefreshed() ?: return true
        val staleThreshold = Duration.ofMinutes(5)
        return clock.instant().isAfter(lastRefreshed.plus(staleThreshold))
    }

}
```

### How To Read It

```text
observeOrders()

↓

Flow from Room. Returns immediately.
The ViewModel subscribes. It receives local data at once.
If Room is empty, it emits an empty list.
No network call. No waiting.

---

refreshOrders()

↓

suspend — runs off the main thread.
Fetches from network. Writes to Room.
Returns Result<Unit> — success or failure.
If network fails, local data is untouched.
Room's Flow above still emits.
The UI still has data.

---

observeLastRefreshed()

↓

Flow of the timestamp of the last successful refresh.
Drives a "Last updated 3 min ago" indicator in the UI.
Shows the user they have cached data, not live data.

---

isStale()

↓

Used by the ViewModel to decide whether to trigger a refresh.
If fresh, skip the network call.
If stale, trigger a background refresh.
```

---

## The ViewModel Side

```kotlin
class OrderHistoryViewModel(
    private val repository: OrderRepository
) : ViewModel() {

    // Local data — always available immediately.
    val orders: StateFlow<List<Order>> =
        repository.observeOrders()
            .stateIn(viewModelScope, SharingStarted.WhileSubscribed(5000), emptyList())

    // Refresh state — drives the UI loading indicator.
    private val _isRefreshing = MutableStateFlow(false)
    val isRefreshing: StateFlow<Boolean> = _isRefreshing

    // Error state — drives the "Could not refresh" snackbar.
    private val _refreshError = MutableStateFlow<String?>(null)
    val refreshError: StateFlow<String?> = _refreshError

    init {
        refresh()
    }

    fun refresh() {
        viewModelScope.launch {
            _isRefreshing.value = true
            repository.refreshOrders()
                .onFailure { _refreshError.value = "Could not update. Showing cached data." }
            _isRefreshing.value = false
        }
    }

}
```

```text
orders StateFlow

↓

Emits local data immediately.
The user sees something before the network responds.

---

refresh()

↓

Runs in background.
Success: Room emits fresh data via orders StateFlow.
Failure: _refreshError emits a message.
         orders StateFlow is unchanged — local data remains.
```

---

## The User Experience Difference

```text
Traditional approach

  Open app → spinner → data (or error)
  Users wait. Users are blocked.

---

Offline-first

  Open app → local data immediately → fresh data (silently)
  Users can act. Users are never blocked by the network.
```

---

## Production Notes

```text
* "Offline-first" does not mean "works fully offline forever."
  It means the app degrades gracefully when connectivity is poor.
  Fresh data still requires a network connection eventually.

* The first launch problem: if the cache is empty and the network fails,
  the user sees an error. This is unavoidable.
  Mitigate it by pre-populating the database with bundled data
  or showing a useful empty state with retry.

* Use Result<Unit> (or a sealed class) for refresh functions.
  Do not throw exceptions from repository refresh calls.
  Uncaught exceptions in a Flow can cancel the entire Flow.

* Pull-to-refresh is a common pattern for manual refresh.
  The SwipeRefreshLayout / PullToRefresh composable maps directly
  to calling repository.refreshOrders().

* "Last updated" indicators are good UX.
  They tell the user the data is cached and how old it is.
  observeLastRefreshed() is the correct pattern for this.

* Stale-while-revalidate: show stale data, refresh in background.
  This is the core idea behind offline-first.
  HTTP has this too. Room + Flow makes it trivial on Android.
```

---

## Common Misconception

A common misconception is:

```text
"Offline-first means the app works fully offline."
```

Incorrect.

```text
Offline-first means:

  ↳ The app shows cached data with no internet.

  ↳ The app does not block the user waiting for the network.

  ↳ When network returns, the data refreshes silently.

  ↳ The user is never stuck on an empty screen
    when cached data exists.
```

What offline-first does NOT mean:

```text
  ↳ Creating new orders without internet (writes require connectivity).

  ↳ Real-time features (chat, live tracking) work offline.

  ↳ The cached data is always current.
```

There is a more advanced pattern for writes without connectivity —

optimistic updates and sync queues.

That is beyond the scope of this concept.

> **You'll see this in...**
> - **DOC 13 — Domain Model vs DTO vs Entity**, where the types flowing through the offline-first pipeline are defined
> - **DOC 14 — Background Work**, where WorkManager is used to schedule network syncs when connectivity returns

---

## A Natural Question

The offline-first pattern works cleanly.

Data flows from the network into Room,

then from Room into the UI.

But DOC 11 returned `UserDto`.

DOC 12 stored `UserEntity`.

DOC 13 exposed `User` to the ViewModel.

Three different classes. All representing the same user.

A natural question appears:

```text
Why Do We Need Three Different Classes
For The Same Data?

Isn't That Just Duplication?
```

That question leads us to:

```text
Domain Model vs DTO vs Entity
```

---

## Revision

### Core Idea

```text
Offline-First =
  Show local data immediately.
  Refresh from network in the background.
  If network fails, local data remains visible.
  The user is never blocked by the network.
```

### The Three States

```text
State 1: Has local data + network succeeds
         → show local immediately, update silently

State 2: Has local data + network fails
         → show local, show "last updated" indicator

State 3: No local data + network fails
         → show error (unavoidable — first launch edge case)
```

### Production Recognition

```kotlin
// ViewModel
val orders = repository.observeOrders()          // immediate local data
    .stateIn(viewModelScope, ...)

init {
    viewModelScope.launch {
        repository.refreshOrders()               // best-effort network sync
            .onFailure { /* show snackbar */ }
    }
}
```

```text
Observe first. Refresh separately.
The database connects them.
```

### Previous Concept

```text
Single Source of Truth
```

### Next Concept

```text
Cache-Then-Network
```
