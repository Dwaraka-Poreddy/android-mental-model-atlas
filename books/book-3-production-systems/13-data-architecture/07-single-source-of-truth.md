# Single Source of Truth

## Looking Back

In the previous concept we saw:

```text
Repository

↓

Coordinates RemoteDataSource + DAO

↓

observe functions  → read from database
refresh functions  → fetch from network, write to database
```

The Repository coordinates two sources.

A natural question appears:

```text
When The UI Needs Data —
Which Source Does It Read From?
```

---

## The Problem

A food delivery app shows a list of restaurants.

The app has two places where restaurant data lives:

```text
Room Database

↓

Restaurants saved locally (from the last time the app ran)

---

Network Response

↓

Fresh restaurants from the API (just fetched)
```

If the UI reads from both:

```text
┌──────────────────────────────────────────────────┐
│                    UI                            │
│              (ObservesViewModel)                 │
└───────────────┬──────────────────────────────────┘
                │
        ┌───────┴───────┐
        │               │
        ↓               ↓
  Room Database    Network Response
  (stale data)     (fresh data)
```

At some point in time,

Room has old data.

The network has new data.

The UI is observing both.

What does the user see?

```text
A spinner that never resolves?

Stale data on one part of the screen, fresh on another?

A flash from old to new?

A list that jumps as two sources resolve at different times?
```

These are real bugs in real apps.

---

## The Mental Model

```text
Single Source of Truth =
  The UI Always Reads From ONE Place.
  The Network Only WRITES To That Place.
  Never Directly To The UI.
```

One source.

One place to read.

One place to trust.

---

## The Wrong Way

```text
ViewModel

↓

reads from Room → gets restaurant list A

AND

reads from network → gets restaurant list B

↓

Shows both? Shows whichever arrives first? Shows whichever is newer?
```

The ViewModel is now trying to reconcile two competing sources of truth.

This always leads to edge cases.

---

## The Right Way

```text
ViewModel

↓

reads from Room only

↓

Room is the single source of truth

---

Network

↓

writes to Room only

↓

Room emits updated data via Flow

↓

ViewModel receives it automatically
```

The network never feeds the UI directly.

The network feeds the database.

The database feeds the UI.

---

## The Flow Of Data

```text
User Opens The App

↓

ViewModel subscribes to Room Flow

↓

Room emits current local data immediately
(even if stale — the user sees something)

          ↓

Repository triggers network refresh (in background)

          ↓

Network returns fresh data

          ↓

Repository writes fresh data to Room

          ↓

Room emits updated data via Flow (automatically)

          ↓

ViewModel receives new emission

          ↓

UI updates — the user sees fresh data
```

The UI made no network call.

The UI observed one source: Room.

Room changed twice.

The UI updated twice.

Both updates came from the same place.

---

## ASCII Diagram — Wrong vs Right

```text
WRONG                              RIGHT

  UI                                UI

  ↑ ↑                               ↑

  │ │                               │

  │ └── Network (fresh)           Room (source of truth)

  │                                 ↑ ↑

  └──── Room (stale)                │ │

                                    │ └── Network writes here

                               Room data (local)
```

On the left:

the UI reads from two places.

Consistency is impossible to guarantee.

On the right:

the UI reads from one place.

The network feeds that one place.

Consistency is automatic.

---

## Minimal Code

```kotlin
class RestaurantRepository(
    private val remoteDataSource: RestaurantRemoteDataSource,
    private val restaurantDao: RestaurantDao
) {

    // UI observes this. Only from Room.
    fun observeRestaurants(): Flow<List<Restaurant>> =
        restaurantDao.observeAll()
            .map { entities -> entities.map { it.toDomain() } }

    // Network writes to Room. Room notifies the Flow above.
    suspend fun refresh() {
        val dtos = remoteDataSource.getRestaurants()
        restaurantDao.replaceAll(dtos.map { it.toEntity() })
    }

}
```

The ViewModel observes `observeRestaurants()`.

The ViewModel also calls `refresh()`.

These are two separate functions.

The database connects them invisibly.

---

## Production Code

A restaurant discovery screen in a food delivery app:

```kotlin
class RestaurantRepository(
    private val remoteDataSource: RestaurantRemoteDataSource,
    private val restaurantDao: RestaurantDao
) {

    // Single source: Room only. Never the network.
    fun observeNearbyRestaurants(
        latitude: Double,
        longitude: Double
    ): Flow<List<Restaurant>> =
        restaurantDao
            .observeByLocation(latitude, longitude)
            .map { entities -> entities.map { it.toDomain() } }

    // Refresh: fetches from network, writes to database.
    // The Flow above emits automatically after this write.
    suspend fun refreshNearbyRestaurants(
        latitude: Double,
        longitude: Double
    ) {
        val dtos = remoteDataSource.getRestaurants(latitude, longitude)
        val entities = dtos.map { it.toEntity() }
        restaurantDao.replaceAll(entities)
    }

    // Search: in production, often done against local data only.
    // The single source of truth applies here too.
    fun searchRestaurants(query: String): Flow<List<Restaurant>> =
        restaurantDao
            .observeByName(query)
            .map { entities -> entities.map { it.toDomain() } }

}
```

### How To Read It

```text
observeNearbyRestaurants()

↓

Returns Flow from Room — the source of truth.
The ViewModel subscribes and receives emissions.
Network is never mentioned here.

---

refreshNearbyRestaurants()

↓

suspend — performs I/O.
Calls the network, gets DTOs.
Maps to Entities.
Writes to Room.
Room's Flow emits automatically.
This function returns Unit — it has no return value.
The return value is the Flow above.

---

searchRestaurants()

↓

Also reads from Room — same single source.
Local search. Fast. No network call.
In production, full search often hits a separate search API.
But the results are still cached locally before being shown.
```

---

## The ViewModel Side

```kotlin
class HomeViewModel(
    private val repository: RestaurantRepository,
    private val locationProvider: LocationProvider
) : ViewModel() {

    private val location = locationProvider.currentLocation

    val restaurants: StateFlow<List<Restaurant>> =
        location.flatMapLatest { loc ->
            repository.observeNearbyRestaurants(loc.lat, loc.lng)
        }.stateIn(viewModelScope, SharingStarted.Eagerly, emptyList())

    init {
        viewModelScope.launch {
            location.collect { loc ->
                repository.refreshNearbyRestaurants(loc.lat, loc.lng)
            }
        }
    }

}
```

```text
restaurants StateFlow

↓

Reads from repository → reads from Room
(never touches the network)

---

init block

↓

Triggers network refresh separately
(writes to Room, which updates the Flow above)
```

Two things happen independently.

One connection: the database.

---

## Production Notes

```text
* The single source of truth is almost always the local database.
  It is always available, even with no internet.

* The database being the source of truth is what enables offline-first.
  The UI works even when the network call has not returned yet.

* OnConflictStrategy.REPLACE is the most common insert strategy here.
  The network data replaces the cached data.
  The Flow emits automatically after the replace.

* replaceAll() (delete all + insert all) is a common pattern for lists.
  It ensures deleted server-side items disappear locally too.

* "Refresh" functions should always be called from a coroutine scope
  with error handling. If the network fails, the local data remains.
  The user still has something to see.
```

---

## Common Misconception

A common misconception is:

```text
"If the UI only reads from Room, the app shows stale data."
```

Partially true, and intentional.

```text
Stale data

↓

Shown immediately when the app opens
(before the network responds)

↓

This is BETTER than showing nothing

↓

The network refresh runs in the background

↓

Room emits fresh data when the write completes

↓

The UI updates automatically
```

The window of staleness is milliseconds to seconds.

The alternative — showing a blank screen while the network loads —

is a significantly worse user experience.

> **You'll see this in...**
> - **DOC 13 — Offline-First Architecture**, where the single source of truth becomes the foundation for full offline support
> - **DOC 13 — Domain Model vs DTO vs Entity**, where the types flowing through these layers are defined

---

## A Natural Question

The single source of truth works well when the network succeeds.

A natural question appears:

```text
What If The User Has No Internet At All?

Does "Single Source of Truth"
Still Give The User Something Useful?
```

That question leads us to:

```text
Offline-First Architecture
```

---

## Revision

### Core Idea

```text
Single Source of Truth =
  UI observes ONE place (the database).
  Network writes TO that place.
  Never to the UI directly.
```

### ASCII Summary

```text
Network Response
    ↓ (writes)
Room Database  ← the single source of truth
    ↓ (Flow)
ViewModel
    ↓ (StateFlow)
UI
```

### Production Recognition

```kotlin
// observe from DB (single source)
fun observeRestaurants(): Flow<List<Restaurant>> =
    dao.observeAll().map { ... }

// refresh from network (writes to DB)
suspend fun refresh() {
    val dtos = remote.getRestaurants()
    dao.replaceAll(dtos.map { it.toEntity() })
}
```

```text
Two functions. One database. One truth.
```

### Previous Concept

```text
Repository Pattern — The Full Picture
```

### Next Concept

```text
Offline-First Architecture
```
