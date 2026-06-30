# Domain Model vs DTO vs Entity

## Looking Back

We have built a complete data pipeline:

```text
Network          →  DOC 11 fetches UserDto from the server
Database         →  DOC 12 stores UserEntity in Room
Repository       →  DOC 13 coordinates both
Offline-First    →  DOC 13 shows cached data immediately
```

Reading production code, you will encounter:

```text
UserDto

UserEntity

User
```

Three different classes.

All representing the same person.

A natural question appears:

```text
Why Three Classes?

Isn't That Just Duplication?
```

---

## The Problem With One Class Everywhere

Imagine using a single `User` class throughout:

```kotlin
data class User(
    val user_id: String,          // server format (snake_case)
    val user_name: String,        // server format
    val user_email: String,       // server format
    @PrimaryKey val id: String,   // Room annotation
    @ColumnInfo(name = "user_id") // Room annotation
    val createdAt: String,        // ISO string from server
    val isActive: Int             // Room stores booleans as Int
)
```

Now this single class must satisfy:

```text
↳ Retrofit: field names must match server JSON keys (snake_case)

↳ Room: needs @PrimaryKey, @ColumnInfo, Int for booleans

↳ ViewModel: needs clean names, proper types, no storage concerns

↳ UI: needs display-ready strings, not raw database values
```

Each layer pulls the class in a different direction.

Adding a new server field breaks the ViewModel.

Optimizing a Room index changes what the UI sees.

The class becomes impossible to reason about.

---

## The Insight

Each layer of the architecture has a different contract:

```text
Network Contract

↓

"Here is the shape the server sends us."
We don't control this. We adapt to it.

---

Storage Contract

↓

"Here is the shape optimized for database queries."
We control this. We optimize for performance.

---

Business Contract

↓

"Here is the shape the app logic uses."
We control this. We optimize for readability.
```

Three different contracts.

Three different classes.

---

## The Mental Model

```text
DTO     = The shape the server sends
          (Network contract — do not change this unilaterally)

Entity  = The shape stored in the database
          (Storage contract — optimize for queries)

Domain  = The shape the app logic uses
          (Business contract — optimize for readability)
```

---

## A Concrete Example

A food delivery app shows a restaurant on screen.

The server sends:

```json
{
  "restaurant_id": "r_1234",
  "restaurant_name": "Biryani House",
  "avg_delivery_time_minutes": 35,
  "is_open": 1,
  "rating_value": 4.3,
  "rating_count": 512
}
```

What the database stores is optimized for querying by location and name.

What the UI needs is clean, display-ready data.

These are three different things.

---

## The Three Classes

### DTO — Network Shape

```kotlin
// Matches the server's JSON exactly.
// Named and typed to match what Retrofit deserializes.
data class RestaurantDto(
    @SerializedName("restaurant_id")
    val restaurantId: String,
    @SerializedName("restaurant_name")
    val restaurantName: String,
    @SerializedName("avg_delivery_time_minutes")
    val avgDeliveryTimeMinutes: Int,
    @SerializedName("is_open")
    val isOpen: Int,                     // server sends Int, not Boolean
    @SerializedName("rating_value")
    val ratingValue: Double,
    @SerializedName("rating_count")
    val ratingCount: Int
)
```

### Entity — Database Shape

```kotlin
// Optimized for Room: indexed columns, proper types, relational keys.
@Entity(
    tableName = "restaurants",
    indices = [Index("name"), Index("latitude", "longitude")]
)
data class RestaurantEntity(
    @PrimaryKey val id: String,
    val name: String,
    val deliveryMinutes: Int,
    val isOpen: Boolean,                 // Room handles Boolean
    val ratingValue: Double,
    val ratingCount: Int,
    val latitude: Double,                // added for local geo queries
    val longitude: Double,               // not in DTO — set separately
    val cachedAt: Long                   // not in DTO — set by the app
)
```

### Domain Model — Business Shape

```kotlin
// Clean Kotlin. No annotations. No platform concerns.
// Used by the ViewModel and composables.
data class Restaurant(
    val id: String,
    val name: String,
    val deliveryTime: String,            // "~35 min" — display-ready
    val isOpen: Boolean,
    val rating: Rating                   // nested type for clarity
)

data class Rating(
    val value: Double,
    val displayText: String              // "4.3 (512 reviews)"
)
```

---

## The Full Pipeline

```text
Server JSON

↓  Retrofit deserializes

RestaurantDto
(snake_case fields, Int for booleans, raw numbers)

↓  Mapper: dto.toEntity()

RestaurantEntity
(Room annotations, indexed, adds latitude/longitude/cachedAt)

↓  Room stores

Room Database

↓  Room's Flow emits

RestaurantEntity (from DB query)

↓  Mapper: entity.toDomain()

Restaurant
(clean names, display-ready strings, no platform annotations)

↓  ViewModel exposes via StateFlow

UI (Composable)
```

---

## The Mappers

```kotlin
// DTO → Entity
fun RestaurantDto.toEntity(
    latitude: Double,
    longitude: Double
): RestaurantEntity = RestaurantEntity(
    id = restaurantId,
    name = restaurantName,
    deliveryMinutes = avgDeliveryTimeMinutes,
    isOpen = isOpen == 1,                // Int → Boolean
    ratingValue = ratingValue,
    ratingCount = ratingCount,
    latitude = latitude,
    longitude = longitude,
    cachedAt = System.currentTimeMillis()
)

// Entity → Domain
fun RestaurantEntity.toDomain(): Restaurant = Restaurant(
    id = id,
    name = name,
    deliveryTime = "~$deliveryMinutes min",
    isOpen = isOpen,
    rating = Rating(
        value = ratingValue,
        displayText = "$ratingValue ($ratingCount reviews)"
    )
)
```

### How To Read It

```text
toEntity()

↓

Converts raw server types to storage-optimized types.
Adds fields the server doesn't know about
(latitude, longitude, cachedAt).
Int isOpen becomes Boolean isOpen.

---

toDomain()

↓

Converts storage types to display types.
"~35 min" is built here, not in the UI.
Rating becomes a nested type for clarity.
No Room annotations. No Retrofit annotations.
Clean Kotlin.
```

---

## Why Each Boundary Protects You

```text
Server changes "restaurant_id" to "id"

↓

Only RestaurantDto changes.
RestaurantEntity is unaffected.
Restaurant (domain) is unaffected.
ViewModel is unaffected.
UI is unaffected.
Only the DTO + mapper changes.

---

You add a new index to the database for geo queries

↓

Only RestaurantEntity changes.
RestaurantDto is unaffected.
Restaurant (domain) is unaffected.
ViewModel is unaffected.
UI is unaffected.
Only the Entity + mapper changes.

---

Designer wants delivery time shown differently

↓

Only toDomain() mapper changes.
RestaurantDto is unaffected.
RestaurantEntity is unaffected.
Nothing else changes.
```

Each boundary insulates the layers from each other.

---

## When To Skip Layers

Not every app needs all three classes.

```text
Small app (1-2 screens, personal project)

↓

Skip the domain model.
Map DTO → Entity.
Use Entity in the ViewModel.
The duplication cost is higher than the benefit.

---

Medium app (production, team of 2-5)

↓

DTO and Entity are almost always separate.
Domain model helps when display logic is complex
(formatting, combining fields, nested types).

---

Large app (multiple teams, multi-module)

↓

All three are always separate.
The modules may not share types across boundaries.
Mappers are the contract between modules.
```

```text
The larger the team and codebase,
the more each boundary is worth the duplication cost.
```

---

## Production Notes

```text
* DTOs belong in the network layer. Never pass a DTO to a ViewModel.

* Entities belong in the data layer. Never pass an Entity to a ViewModel.

* Domain Models belong to the business layer. They have no annotations.

* Extension functions (toEntity(), toDomain()) are the most common
  mapper pattern in Kotlin Android codebases.

* A DTO field you don't use yet should still be in the DTO.
  Ignore it in the mapper. It costs nothing. Removing it later
  is harder than keeping it.

* If the server type and storage type are identical (rare),
  it is acceptable to use the same class for DTO and Entity.
  Add @Entity and @SerializedName. This is a pragmatic shortcut,
  not a violation.

* The domain model is often called the "clean model" or "business model."
  It is always free of platform-specific imports.
```

---

## Common Misconception

A common misconception is:

```text
"Three classes for one thing is over-engineering."
```

In a small personal project, that may be true.

In a production team codebase:

```text
One class for everything

↓

Server format leaks into the UI.
Database schema leaks into business logic.
A server change breaks the UI directly.
A database index change requires updating ViewModels.
Display formatting logic ends up in the database layer.
```

The "duplication" of three classes

prevents a much worse duplication:

```text
Business logic duplicated in every layer
because there is no clean place to put it.
```

> **You'll see this in...**
> - **DOC 13 — Data Mapping**, where mapping strategies are explored in detail
> - **DOC 14 — Dependency Injection**, where Hilt provides mappers as dependencies

---

## A Natural Question

Writing these mappers by hand is repetitive.

`toEntity()`, `toDomain()`, `toDto()` —

they appear in every domain in the codebase.

A natural question appears:

```text
Is There A Better Way To Write Mappers?

How Do Production Teams Organize All This Mapping Code?
```

That question leads us to:

```text
Data Mapping
```

---

## Revision

### Core Idea

```text
DTO     = Network contract  (server's shape)
Entity  = Storage contract  (database's shape)
Domain  = Business contract (app's shape)
```

### The Pipeline

```text
Network → DTO → [toEntity()] → Entity → Room → [toDomain()] → Domain → ViewModel
```

### Production Recognition

```kotlin
data class RestaurantDto(...)   // @SerializedName annotations

@Entity data class RestaurantEntity(...)  // @PrimaryKey annotations

data class Restaurant(...)      // no annotations — clean Kotlin

fun RestaurantDto.toEntity()    // maps between contracts
fun RestaurantEntity.toDomain() // maps between contracts
```

### When To Skip

```text
Small app or personal project

↓

Entity used as Domain Model is acceptable.
Skip toDomain(). Use the Entity in the ViewModel.
```

### Previous Concept

```text
Network-Then-Cache
```

### Next Concept

```text
Data Mapping
```
