# Why Local Persistence Exists

## Looking Back

In DOC 11 we learned how Android applications communicate with servers.

```text
Application

↓

OkHttp / Retrofit

↓

Server

↓

Response
```

The application can now fetch data from anywhere in the world.

A natural question appears:

```text
What Happens To That Data After The App Closes?
```

---

## A Simple Observation

Imagine a food delivery application.

The user opens the app.

```text
Open App

↓

Fetch Restaurants

↓

Loading Spinner...

↓

Restaurants Appear
```

The user browses the list.

Then they close the app.

A few minutes later they open it again.

```text
Open App

↓

Fetch Restaurants

↓

Loading Spinner...

↓

Restaurants Appear
```

The exact same loading spinner.

The exact same network request.

The exact same wait.

Every single time.

---

## The Problem Gets Worse

Now imagine the user has no internet connection.

```text
Open App

↓

Fetch Restaurants

↓

No Internet

↓

Empty Screen
```

The app has nothing to show.

Not because the data doesn't exist.

But because the data only existed in memory,

and memory is cleared when the app closes.

```text
App Running
↓
Data In Memory

App Closed
↓
Memory Cleared
↓
Data Gone
```

---

## What Users Actually Experience

Consider what this means across a real application.

```text
Open App               → Loading Spinner
Switch Tabs            → Loading Spinner
Rotate Device          → Loading Spinner
Return From Background → Loading Spinner
No Internet            → Empty Screen
```

Every interaction starts from zero.

The app has no memory.

---

## The Mental Model

Local persistence is the application's memory.

```text
Without Persistence

↓

App Forgets Everything When Closed

---

With Persistence

↓

App Remembers Data Between Sessions
```

Think of it this way.

A person without memory must re-learn everything each morning.

A person with memory wakes up and continues where they left off.

Local persistence gives the application that same ability.

---

## What "Local" Means

```text
Remote Data

↓

Lives On A Server

↓

Requires Network To Access

---

Local Data

↓

Lives On The Device

↓

Available Immediately
```

"Local" means the data is stored on the physical device itself.

No network request is needed to read it.

---

## The Before And After

Without local persistence:

```text
User Opens App

↓

Network Request

↓

Wait

↓

Display Data
```

With local persistence:

```text
User Opens App

↓

Read Local Data

↓

Display Immediately

↓

(Refresh From Network In Background)
```

The user sees data instantly.

The network request happens quietly,

updating the local data when it completes.

---

## A Real Example

Consider a chat application.

Without persistence:

```text
Open App       → Fetch all conversations from server
Close App      → All conversations gone
Open App Again → Fetch all conversations again
No Internet    → No conversations visible
```

With persistence:

```text
Open App       → Show saved conversations immediately
Close App      → Conversations remain on device
Open App Again → Conversations appear instantly
No Internet    → All previous conversations still visible
```

The user never sees an empty screen.

---

## Why This Matters For Production Applications

Every production Android application uses local persistence.

Not as an optimization.

As a requirement.

```text
User Expectation

↓

App Should Work Offline

↓

App Should Load Instantly

↓

App Should Remember My Data
```

Users do not distinguish between "the app" and "the server."

They expect the app to simply work.

---

## Where Does Data Get Stored?

Android provides several storage mechanisms.

```text
Memory (RAM)

↓

Gone When App Closes

---

Files

↓

Persist On Disk

---

Key-Value Storage

↓

Simple Settings And Preferences

---

Database

↓

Structured Data With Queries
```

Each mechanism serves a different purpose.

For most application data,

a database is the right choice.

---

## Minimal Code

The simplest form of persistence in Android:

```kotlin
val prefs = context
    .getSharedPreferences("settings", MODE_PRIVATE)

prefs.edit()
    .putString("username", "dwaraka")
    .apply()
```

This stores a single key-value pair on the device.

It survives app restarts.

---

## Production Code

In a production application,

persistence typically involves a database.

```kotlin
class RestaurantRepository(
    private val api: RestaurantApi,
    private val dao: RestaurantDao
) {

    fun getRestaurants(): Flow<List<Restaurant>> {
        return dao.getAll()
    }

    suspend fun refresh() {
        val remote = api.fetchRestaurants()
        dao.insertAll(remote)
    }

}
```

### How To Read It

```text
RestaurantRepository

↓

getRestaurants()

↓

Read From Local Database (Instant)

---

refresh()

↓

Fetch From Server

↓

Save To Local Database
```

The UI always reads from the local database.

The network updates the local database.

This pattern appears in nearly every production Android application.

---

## Production Notes

```text
* Every production app persists data locally.

* Local reads are instant. Network reads take time.

* The user should never see a loading spinner for data they've already seen.

* Offline support is not a feature. It is a baseline expectation.

* The most common local storage for structured data is a database.

* Key-value storage (SharedPreferences, DataStore) is for simple settings, not application data.
```

---

## Common Misconception

A common misconception is:

```text
Local Persistence = Caching
```

Incorrect.

```text
Cache

↓

Temporary Copy For Performance

---

Local Persistence

↓

The Authoritative Local Copy Of Application Data
```

A cache can be cleared at any time.

Local persistence is the foundation of the application's data layer.

They may use the same storage technology,

but they serve different architectural purposes.

---

## Putting Everything Together

```text
DOC 11

↓

Fetch Data From Server

---

DOC 12

↓

Store Data On Device

---

Together

↓

Fetch Once, Read Many Times
```

This is the bridge between remote communication and a responsive application.

> **You'll see this in...**
> - **DOC 11 -- Data Architecture**, where the Repository pattern decides when to read locally and when to refresh from the network
> - **DOC 14 -- Background Work**, where WorkManager syncs local data with the server even when the app is closed

---

## A Natural Question

We know the application needs to store data on the device.

We know a database is the right tool for structured data.

But what exactly is a database?

How does it organize information?

That question leads us to the next concept:

```text
Database Fundamentals
```

---

## Revision

### Core Idea

```text
Local Persistence = The App's Memory
```

### Mental Model

```text
Server

↓

Network (Slow, Unreliable)

↓

Local Database (Fast, Always Available)

↓

UI
```

### Production Recognition

```kotlin
dao.getAll()
```

↓

```text
Read From Local Storage — Instant, No Network Needed
```

### Previous Concept

```text
DOC 11 — Remote Communication
```

### Next Concept

```text
Database Fundamentals
```
