# Repository Pattern — The Full Picture

## Looking Back

In DOC 5 we first encountered the Repository:

```text
Repository

↓

The Owner Of Application Data
```

In DOC 11 we built a RemoteDataSource:

```text
RemoteDataSource

↓

The Single Entry Point For Network Data
```

In DOC 12 we built a DAO:

```text
DAO

↓

The Interface For Database Operations
```

Now we have all the pieces.

A natural question appears:

```text
How Do They All Work Together?
```

---

## The Question DOC 5 Left Open

DOC 5 introduced the Repository as a concept.

It showed:

```text
ViewModel → Repository → Data
```

But it did not show what happens inside the Repository.

It did not show how a Repository handles a network response.

It did not show how a Repository writes to Room.

It did not show how a Repository decides which source to use.

This is that document.

---

## The Repository As Coordinator

Think of a restaurant's head chef.

```text
Guests (ViewModels)

↓

"I need a pasta dish"

↓

Head Chef (Repository)

↓

Decides: pasta station or pre-made batch?

↓

Pasta Station (Network)        Prep Shelf (Database)
(If fresh is needed)           (If something's ready)

↓

Plates the dish

↓

Returns it to the guests
```

The guests do not walk into the kitchen.

The guests do not know whether the pasta was freshly made

or pulled from a pre-prepared batch.

The head chef coordinates that decision.

---

## The Mental Model

```text
Repository = The Coordinator That Decides:

  1. When to read from the network
  2. When to read from the cache (database)
  3. How to keep them in sync
```

The Repository owns a type of data.

`UserRepository` owns everything user-related.

`OrderRepository` owns everything order-related.

Nothing else in the app touches the data directly.

---

## One Owner For One Domain

```text
UserRepository

↓

Owns All User Data

↓

┌──────────────────────────────────────┐
│  getUserById(id)                     │
│  observeCurrentUser()                │
│  updateProfile(name)                 │
│  signOut()                           │
│  refreshFromNetwork()                │
└──────────────────────────────────────┘
```

If you want user data,

you go to `UserRepository`.

If you want to update a user,

you go to `UserRepository`.

There is no other way in.

---

## Minimal Code

```kotlin
class UserRepository(
    private val remoteDataSource: UserRemoteDataSource,
    private val userDao: UserDao
) {

    fun observeUser(id: String): Flow<User> =
        userDao.observeById(id)

    suspend fun refresh(id: String) {
        val dto = remoteDataSource.getUser(id)
        userDao.insert(dto.toEntity())
    }

}
```

Eleven lines.

The entire coordination strategy visible at a glance.

---

## Production Code

A real `UserRepository` from a food delivery application:

```kotlin
class UserRepository(
    private val remoteDataSource: UserRemoteDataSource,
    private val userDao: UserDao
) {

    // Observe — always reads from the local database.
    // The database is the single source of truth.
    fun observeCurrentUser(): Flow<User?> =
        userDao.observeCurrentUser()
            .map { entity -> entity?.toDomain() }

    // Refresh — fetches from network, writes to database.
    // The Flow above will emit automatically when the write happens.
    suspend fun refreshCurrentUser() {
        val dto = remoteDataSource.getCurrentUser()
        userDao.insertOrReplace(dto.toEntity())
    }

    // One-time read — used when a snapshot is needed, not a stream.
    suspend fun getUserById(id: String): User? =
        userDao.getById(id)?.toDomain()
            ?: fetchAndCacheUser(id)

    // Update — writes to network first, then reflects locally.
    suspend fun updateDisplayName(name: String) {
        val updatedDto = remoteDataSource.updateDisplayName(name)
        userDao.insertOrReplace(updatedDto.toEntity())
    }

    // Private helper — not part of the public contract.
    private suspend fun fetchAndCacheUser(id: String): User? {
        val dto = remoteDataSource.getUser(id) ?: return null
        val entity = dto.toEntity()
        userDao.insertOrReplace(entity)
        return entity.toDomain()
    }

}
```

### How To Read It

```text
observeCurrentUser()

↓

Returns Flow — The ViewModel observes this.
Source: local database only.
(The database is always the source of truth for observation.)

---

refreshCurrentUser()

↓

suspend — Does real I/O work.
Fetches from network → writes to database.
The Flow from observeCurrentUser() emits automatically.
(Network refreshes the cache. The cache feeds the UI.)

---

getUserById(id)

↓

Try the database first.
If not found, fetch from network and cache it.
Return the domain model either way.
(The caller gets data — they don't know which source it came from.)

---

updateDisplayName(name)

↓

Write to network first (server is authoritative).
Then reflect the change in the local cache.
(Network-first for writes. Cache-first for reads.)

---

fetchAndCacheUser(id)

↓

Private helper. Not visible to the ViewModel.
The implementation detail is hidden.
```

---

## The Observation Pattern

Notice the split between:

```text
observeCurrentUser()  ← returns Flow (ongoing stream)

refreshCurrentUser()  ← suspend (one-time action)
```

This is intentional.

The ViewModel calls both separately:

```kotlin
class ProfileViewModel(
    private val repository: UserRepository
) : ViewModel() {

    val user: StateFlow<User?> =
        repository.observeCurrentUser()
            .stateIn(viewModelScope, SharingStarted.Eagerly, null)

    init {
        viewModelScope.launch {
            repository.refreshCurrentUser()
        }
    }

}
```

```text
observeCurrentUser() → started immediately, streams forever

refreshCurrentUser() → launched once in init, then on pull-to-refresh
```

The observation and the refresh are independent.

The database connects them.

---

## The Repository's Contract

The ViewModel sees only:

```text
fun observeCurrentUser(): Flow<User?>

suspend fun refreshCurrentUser()

suspend fun getUserById(id: String): User?

suspend fun updateDisplayName(name: String)
```

The ViewModel does not see:

```text
UserRemoteDataSource
UserApi
UserDao
UserEntity
UserDto
.toEntity()
.toDomain()
OnConflictStrategy
```

Those are all hidden.

The Repository's contract is a clean Kotlin interface.

The implementation is an engineering concern.

---

## Production Notes

```text
* The Repository is the only class that knows about both
  RemoteDataSource and DAO simultaneously.

* The Repository's public API uses Domain Models (User),
  not DTOs (UserDto) or Entities (UserEntity).
  Callers should never see storage or network types.

* observe functions return Flow.
  action functions are suspend.
  This distinction is always intentional.

* A Repository for one domain (UserRepository) should not
  know about another domain (OrderRepository).
  Cross-domain coordination belongs in a UseCase.

* Private helpers keep the public contract small and clear.
  If a function is not called from outside, make it private.

* The Repository is a great place to add logging and
  analytics — it's the single place where data changes happen.
```

---

## Common Misconception

A common misconception is:

```text
Repository = Network Fetcher That Also Caches
```

Incorrect.

```text
Repository = Coordinator

↓

Sometimes reads locally without touching the network at all.
Sometimes writes to the network and ignores the local cache.
Sometimes reads locally AND refreshes in the background.
Sometimes reads network first and falls back to local.

The strategy is chosen per-operation, not per-repository.
```

The Repository is not a single caching strategy.

It makes per-operation decisions.

> **You'll see this in...**
> - **DOC 13 — Single Source of Truth**, where we see why the ViewModel always observes the database, never the network directly
> - **DOC 13 — Offline-First Architecture**, where the Repository handles no-internet scenarios gracefully
> - **DOC 14 — Dependency Injection**, where Hilt provides Repository instances to ViewModels automatically

---

## A Natural Question

The Repository coordinates two sources.

The observation pattern reads from the database.

The refresh writes to the database.

A natural question appears:

```text
When The App Has Both A Local Cache And Fresh Network Data —
Which One Should The UI Display?

If They Show Different Data At The Same Time,
Is That A Bug?
```

That question leads us to:

```text
Single Source of Truth
```

---

## Revision

### Core Idea

```text
Repository = The Coordinator That Owns A Domain's Data

  Decides: network or cache?
  Hides:   all implementation details
  Exposes: clean Kotlin functions with Domain Models
```

### Mental Model

```text
ViewModel

↓ (Flow / suspend)

Repository (coordinator)

↓                    ↓

RemoteDataSource    DAO
(network)           (database)

↓                    ↓

Retrofit            Room
```

### Production Recognition

```kotlin
class UserRepository(
    private val remoteDataSource: UserRemoteDataSource,
    private val userDao: UserDao
) {
    fun observeCurrentUser(): Flow<User?> = ...  // observe from DB
    suspend fun refreshCurrentUser() = ...        // refresh from network
}
```

```text
Two Dependencies. Two Responsibilities. One Coordinator.
```

### Previous Concept

```text
Local Data Source
```

### Next Concept

```text
Single Source of Truth
```
