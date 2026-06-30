# Room + Flow

## Looking Back

In the previous concepts we learned:

```text
Entity

↓

Describes A Table

---

DAO

↓

Defines Operations On That Table
```

We saw that a DAO can return Flow:

```kotlin
@Query("SELECT * FROM orders")
fun observeOrders(): Flow<List<OrderEntity>>
```

A natural question appears:

```text
What Actually Happens When The Database Changes?
```

Does the UI really update automatically?

---

## The Problem

Imagine a chat application.

New messages arrive.

The database gets updated.

But the UI is still showing the old messages.

```text
Database: 5 messages

UI: 3 messages (stale)
```

One approach:

```text
Insert Message

↓

Manually Tell The UI To Refresh

↓

UI Queries The Database Again

↓

UI Updates
```

This works,

but it requires you to manually coordinate every change.

Every insert needs a refresh call.

Every update needs a refresh call.

Every delete needs a refresh call.

Miss one,

and the UI goes stale.

---

## What We Actually Want

```text
Database Changes

↓

UI Updates Automatically

↓

No Manual Coordination
```

The database should push updates to the UI

whenever data changes.

---

## Room + Flow

When a DAO method returns Flow,

Room automatically observes the underlying table.

```kotlin
@Query("SELECT * FROM messages ORDER BY timestamp DESC")
fun observeMessages(): Flow<List<MessageEntity>>
```

This is not a one-time read.

This is a live connection.

```text
observeMessages()

↓

Flow<List<MessageEntity>>

↓

Emits Current Data Immediately

↓

Emits Again After Every INSERT

↓

Emits Again After Every UPDATE

↓

Emits Again After Every DELETE
```

---

## The Mental Model

Think of it as a security camera.

```text
One-Time Query (suspend fun)

↓

Take A Photograph

↓

Captures One Moment

---

Flow Query (fun returning Flow)

↓

Security Camera

↓

Continuously Shows What's Happening
```

A photograph shows what the room looked like at one point.

A security camera shows what the room looks like right now,

and updates the moment anything changes.

---

## How It Works

```text
Collector Subscribes
       ↓
  ┌────────────┐
  │ Room runs   │
  │ SELECT *    │──→ Emits [A, B, C]
  │ FROM msgs   │
  └────────────┘
       ↓
  (Table changes: INSERT D)
       ↓
  ┌────────────┐
  │ Room re-runs│
  │ SELECT *    │──→ Emits [A, B, C, D]
  │ FROM msgs   │
  └────────────┘
       ↓
  (Table changes: DELETE B)
       ↓
  ┌────────────┐
  │ Room re-runs│
  │ SELECT *    │──→ Emits [A, C, D]
  │ FROM msgs   │
  └────────────┘
```

Every table modification triggers a fresh query.

Every fresh query emits a new result through the Flow.

---

## Connecting To What You Already Know

This concept bridges three earlier docs.

```text
DOC 2 — Coroutines

↓

suspend fun for one-time database operations

---

DOC 3 — Flow

↓

Flow for continuous observation of database changes

---

DOC 4 — StateFlow

↓

stateIn converts the database Flow to StateFlow for the UI
```

The tools are the same.

The context is new.

---

## Minimal Code

```kotlin
// DAO
@Query("SELECT * FROM users")
fun observeUsers(): Flow<List<UserEntity>>

// Repository
val users: Flow<List<User>> =
    userDao.observeUsers()
        .map { entities -> entities.map { it.toDomain() } }

// ViewModel
val users: StateFlow<List<User>> =
    repository.users
        .stateIn(viewModelScope, SharingStarted.WhileSubscribed(5000), emptyList())
```

Three layers.

Each one you already understand.

---

## The Full Chain

```text
SQLite Table

↓

Room Observes Changes

↓

Flow<List<Entity>> (DOC 3)

↓

Repository Maps To Domain Model

↓

StateFlow (DOC 4)

↓

ViewModel Exposes To UI

↓

Composable / Fragment Collects

↓

Screen Updates
```

When a row is inserted into the database,

the screen updates.

No manual refresh.

No callback.

No event bus.

The data flows from the database to the screen automatically.

---

## Production Code

A complete example from a food delivery app:

```kotlin
// DAO
@Dao
interface RestaurantDao {

    @Query("SELECT * FROM restaurants ORDER BY rating DESC")
    fun observeAll(): Flow<List<RestaurantEntity>>

    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insertAll(restaurants: List<RestaurantEntity>)

}

// Repository
class RestaurantRepository(
    private val dao: RestaurantDao,
    private val api: RestaurantApi
) {

    val restaurants: Flow<List<Restaurant>> =
        dao.observeAll()
            .map { entities ->
                entities.map { it.toDomainModel() }
            }

    suspend fun refresh() {
        val remote = api.getRestaurants()
        dao.insertAll(remote.toEntities())
    }

}

// ViewModel
class HomeViewModel(
    private val repository: RestaurantRepository
) : ViewModel() {

    val restaurants: StateFlow<List<Restaurant>> =
        repository.restaurants
            .stateIn(
                viewModelScope,
                SharingStarted.WhileSubscribed(5000),
                emptyList()
            )

    init {
        viewModelScope.launch {
            repository.refresh()
        }
    }

}
```

### How To Read It

```text
dao.observeAll()

↓

Flow — Emits Whenever The Table Changes

---

repository.restaurants

↓

Maps Entity To Domain Model

(Database details stay in the data layer)

---

stateIn(viewModelScope, ...)

↓

Converts Flow To StateFlow (DOC 4)

(UI observes this)

---

repository.refresh()

↓

Fetches From Server, Saves To Database

↓

Database Change Triggers Flow Emission

↓

UI Updates Automatically
```

Notice the refresh flow:

```text
Network Response

↓

dao.insertAll(...)

↓

Table Changes

↓

Room Re-runs Query

↓

Flow Emits New Data

↓

StateFlow Updates

↓

UI Recomposes
```

The ViewModel never manually updates the UI after a network call.

The database is the single source of truth.

Inserting into the database is enough.

The Flow carries the update to the screen.

---

## Production Notes

```text
* Room Flow queries re-execute on ANY write to the observed table, not just the rows in the result.

* Use stateIn to convert Room's Flow to StateFlow in the ViewModel. The UI collects from StateFlow.

* SharingStarted.WhileSubscribed(5000) keeps the query active for 5 seconds after the last collector leaves. This survives configuration changes.

* Never collect a Room Flow on the main thread. Use flowOn(Dispatchers.IO) or rely on Room's built-in dispatcher.

* The Repository should map Entities to domain models. The ViewModel should never see Entity classes.

* This pattern — observe locally, refresh from network — is the foundation of offline-first architecture.
```

---

## Common Misconception

A common misconception is:

```text
Room Flow Only Emits When My Specific Query's Rows Change
```

Incorrect.

```text
Room Flow

↓

Re-emits On ANY Write To The Table

↓

Even If The Write Doesn't Affect The Query's Rows
```

If you observe users with `status = 'active'`

and someone inserts a user with `status = 'inactive'`,

Room still re-runs your query.

It does not track which rows match.

It tracks which tables are touched.

This is usually fine,

but it means you should not assume each emission represents a meaningful change.

---

> **You'll see this in...**
> - **DOC 11 -- Data Architecture**, where this pattern becomes the standard Repository implementation: observe locally, refresh from network
> - **DOC 14 -- Background Work**, where WorkManager writes to the database and the UI automatically reflects the changes through this same Flow chain

---

## A Natural Question

We now know that Room and Flow together give us reactive reads.

The database pushes changes to the UI automatically.

But what about writes?

When the user places an order,

that write goes to the database.

How do we make sure writes happen safely,

off the main thread,

with proper error handling?

That question leads us to:

```text
Room + Coroutines
```

---

## Revision

### Core Idea

```text
Room + Flow = The Database Pushes Updates To The UI Whenever Data Changes
```

### Mental Model

```text
Table Changes

↓

Room Re-runs Query

↓

Flow Emits New Result

↓

StateFlow Updates

↓

UI Recomposes
```

### Production Recognition

```kotlin
fun observeOrders(): Flow<List<OrderEntity>>
```

↓

```text
Live Database Observation — UI Stays In Sync Automatically
```

### Previous Concept

```text
TypeConverters
```

### Next Concept

```text
Room + Coroutines
```
