# Why Data Architecture Exists

## Looking Back

In DOC 11 we learned:

```text
Retrofit

↓

API Service Interface

↓

Remote Data Source

↓

Network Data Arrives As DTOs
```

In DOC 12 we learned:

```text
Room

↓

Entity + DAO

↓

Local Data Persists In SQLite
```

Two powerful tools.

A natural question appears:

```text
How Do We Use Them Together?
```

---

## A Tempting Shortcut

You have Retrofit.

You have Room.

Why not use them directly from the ViewModel?

```kotlin
class OrdersViewModel : ViewModel() {

    private val api = OrderApi.create()
    private val db  = AppDatabase.getInstance()

    fun refresh() {
        viewModelScope.launch {
            val orders = api.getOrders()
            db.orderDao().insertAll(orders)
            _uiState.value = OrdersUiState(orders)
        }
    }

}
```

This works.

At first.

---

## The Problem

Count what that ViewModel knows:

```text
↳ How to create a Retrofit API instance

↳ How to create a Room database instance

↳ Which API endpoint to call

↳ How to map a network response into a database row

↳ When to use the cache and when to hit the network

↳ How to handle HTTP errors vs. database errors

↳ What the caching strategy is
```

Seven different concerns.

In one class.

The ViewModel's only job was:

```text
Build Screen State
```

---

## Why This Matters At Scale

One screen may be manageable.

But a real food delivery app has:

```text
HomeScreen
OrderTrackingScreen
OrderHistoryScreen
RestaurantMenuScreen
CartScreen
ProfileScreen
SearchScreen
```

Each one making direct Retrofit calls.

Each one querying Room directly.

Each one implementing its own caching strategy.

```text
HomeViewModel         → api.getRestaurants() + db.restaurantDao()
OrderTrackingViewModel → api.getOrder(id)    + db.orderDao()
OrderHistoryViewModel  → api.getOrders()     + db.orderDao()
CartViewModel          → api.getCart()       + db.cartDao()
ProfileViewModel       → api.getProfile()    + db.userDao()
```

Now changing the caching strategy means touching five files.

A bug in error handling appears in five places.

There is no single place to understand what data the app uses.

---

## The Real Cost

```text
Without A Data Layer

↓

Every ViewModel Is Its Own Data Pipeline

↓

Changes Ripple Everywhere

↓

The Codebase Becomes Difficult To Change

↓

Bugs Are Hard To Isolate
```

---

## The Insight

The rest of the app does not care:

```text
↳ Whether data came from the network

↳ Whether data came from the local database

↳ Whether data was cached five minutes ago

↳ Whether three API calls were combined to produce it
```

The rest of the app only cares:

```text
Give Me The Current Orders
```

---

## The Mental Model

```text
Data Architecture Exists To Give The Rest Of The App
ONE Clean Interface For Data —
Regardless Of Where That Data Comes From.
```

Think of a power outlet.

```text
Your Phone (The App)

↓

The Outlet (The Data Layer)

↓

Power Plant + Cables + Transformers
(Network + Database + Cache Strategy)
```

You plug in.

You get power.

You do not need to know how it was generated,

transmitted,

or stepped down in voltage.

---

## The Data Layer As A Black Box

```text
ViewModel

↓

"Give me the current orders"

↓

┌──────────────────────────────────────────────┐
│                DATA LAYER                    │
│                                              │
│  ┌──────────────────┐  ┌──────────────────┐  │
│  │  Remote Data     │  │  Local Data      │  │
│  │  Source          │  │  Source (DAO)    │  │
│  │  (Retrofit)      │  │  (Room)          │  │
│  └────────┬─────────┘  └────────┬─────────┘  │
│           │                     │            │
│           └──────────┬──────────┘            │
│                      │                       │
│                 Repository                   │
│                 (Coordinator)                │
└──────────────────────┬───────────────────────┘

↓

Orders (clean, ready to display)
```

The ViewModel asks.

The data layer decides how to answer.

The ViewModel does not care about the internals.

---

## The Three Actors

```text
Remote Data Source

↓

Owns all network communication.
Wraps Retrofit API calls.
Translates network errors.

---

Local Data Source (DAO)

↓

Owns all database communication.
Wraps Room queries.
Returns Flow or suspend results.

---

Repository

↓

Coordinates the above two.
Decides: network or cache?
Presents one clean interface to the ViewModel.
```

---

## What Changes With A Data Layer

```text
Without Data Layer

  ViewModel → Retrofit + Room + caching + error handling
              (seven concerns in one class)

---

With Data Layer

  ViewModel → Repository only
              ("Give me orders" is the entire relationship)

  Repository → RemoteDataSource + DAO
               (coordinates them, hides the details)

  RemoteDataSource → Retrofit only

  DAO → Room only
```

Each class knows about exactly one thing.

---

## Production Notes

```text
* The data layer is a boundary.
  What lives inside it can change
  without the ViewModel knowing.

* Swapping REST for GraphQL?
  Only the Remote Data Source changes.

* Swapping Room for a different local store?
  Only the Local Data Source changes.

* The ViewModel is not aware of either change.

* Well-designed data layers make the app testable.
  You swap real data sources for fakes in tests.

* The data layer is not a folder —
  it is a responsibility boundary.
```

---

## Common Misconception

A common misconception is:

```text
"The ViewModel can call the Repository for some things
 and the DAO directly for others."
```

Incorrect.

```text
ViewModel

↓

Repository (only)

↓

DAO + RemoteDataSource (the Repository coordinates these)
```

The ViewModel should not know the DAO exists.

The DAO is an implementation detail of the Repository.

If a ViewModel needs data from the database,

that data is exposed through a Repository,

never by calling the DAO from the ViewModel directly.

> **You'll see this in...**
> - **DOC 13 — Repository Pattern**, where we implement the coordinator in full
> - **DOC 13 — Single Source of Truth**, where we see the payoff of this boundary
> - **DOC 14 — Dependency Injection**, where Hilt wires all these layers together automatically

---

## A Natural Question

We now understand why a data layer needs to exist.

We understand it needs a coordinator.

A natural question appears:

```text
Who Is The Coordinator?

How Does It Decide When To Hit The Network
And When To Use The Cache?
```

That coordinator is the Repository.

That question leads us to:

```text
Repository Pattern — The Full Picture
```

---

## Revision

### Core Idea

```text
Data Architecture = ONE Clean Interface For Data
                    Regardless Of Source
```

### Mental Model

```text
ViewModel

↓

Data Layer (black box)

↓

Network + Database (implementation details)
```

### Production Recognition

```kotlin
class OrdersViewModel(
    private val repository: OrderRepository  // ← only data dependency
) : ViewModel()
```

```text
No Retrofit.
No Room.
Only the Repository.
```

### Previous Concept

```text
DOC 12 — Room + Flow
```

### Next Concept

```text
The Data Layer
```
