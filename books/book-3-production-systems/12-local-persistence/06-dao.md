# DAO

## Looking Back

In the previous concept we learned:

```text
Entity

↓

A Data Class That Maps To A Database Table

↓

Property = Column

↓

Instance = Row
```

We can now describe a table in Kotlin.

But a table with no operations is useless.

A natural question appears:

```text
How Do We Read And Write Data?
```

---

## The Problem

We have a table:

```text
┌────────┬──────────────┬─────────────────────┐
│ userId │ name         │ email               │
├────────┼──────────────┼─────────────────────┤
│ 1      │ Dwaraka      │ dwaraka@example.com │
│ 2      │ Priya        │ priya@example.com   │
└────────┴──────────────┴─────────────────────┘
```

We need to:

```text
Get all users

Get one user by ID

Insert a new user

Update an existing user

Delete a user
```

Without Room,

each of these requires writing raw SQL,

managing cursors,

handling errors,

and closing resources manually.

With Room,

we describe what we want.

Room handles how.

---

## DAO

**DAO** stands for Data Access Object.

It is an interface that defines the operations allowed on a table.

```text
DAO

↓

"Here Are The Operations I Allow On This Data"
```

You write the interface.

Room generates the implementation.

---

## The Mental Model

Think of a DAO as a librarian.

```text
You (The Application)

↓

"I need all books by this author"

↓

Librarian (DAO)

↓

Knows How To Find Them

↓

Returns The Books
```

You describe what you want.

The librarian knows how to get it.

You never walk into the shelves yourself.

---

## Minimal Code

```kotlin
@Dao
interface UserDao {

    @Query("SELECT * FROM users")
    fun getAll(): List<UserEntity>

    @Insert
    fun insert(user: UserEntity)

}
```

Two operations.

One reads all users.

One inserts a user.

Room generates the implementation for both.

---

## The Four Core Annotations

### @Query

```text
@Query

↓

"Run This SQL And Return The Result"
```

```kotlin
@Query("SELECT * FROM users WHERE userId = :id")
fun getById(id: String): UserEntity?
```

Notice `:id` in the SQL.

Room binds the function parameter to the query parameter.

No string concatenation.

No SQL injection risk.

---

### @Insert

```text
@Insert

↓

"Insert This Object As A New Row"
```

```kotlin
@Insert
fun insert(user: UserEntity)
```

Room generates the INSERT statement from the Entity's properties.

You never write INSERT SQL.

---

### @Update

```text
@Update

↓

"Update The Row That Matches This Object's Primary Key"
```

```kotlin
@Update
fun update(user: UserEntity)
```

Room finds the row by primary key and updates all columns.

---

### @Delete

```text
@Delete

↓

"Delete The Row That Matches This Object's Primary Key"
```

```kotlin
@Delete
fun delete(user: UserEntity)
```

Room finds the row by primary key and removes it.

---

## The Pattern

```text
@Query   → Custom SQL (SELECT, complex queries)

@Insert  → No SQL needed (Room generates it)

@Update  → No SQL needed (Room generates it)

@Delete  → No SQL needed (Room generates it)
```

You only write SQL for reads and custom operations.

For standard writes,

Room handles everything.

---

## Connecting To Coroutines

Remember from DOC 2:

```text
suspend Function

↓

May Suspend A Coroutine
```

Database operations are I/O.

They should not block the main thread.

Room integrates directly with coroutines:

```kotlin
@Insert
suspend fun insert(user: UserEntity)

@Query("SELECT * FROM users WHERE userId = :id")
suspend fun getById(id: String): UserEntity?
```

The `suspend` keyword you learned in DOC 2 appears here.

Same concept.

Different context.

```text
DOC 2: suspend fun fetchUser()   → Network I/O

DOC 12: suspend fun insert(user) → Database I/O
```

Both are I/O operations.

Both use `suspend` to avoid blocking the main thread.

---

## Connecting To Flow

Remember from DOC 3:

```text
Flow

↓

A Stream Of Values Over Time
```

Room can return a Flow instead of a single value:

```kotlin
@Query("SELECT * FROM users")
fun getAll(): Flow<List<UserEntity>>
```

This is not a one-time read.

This is a stream that emits a new list every time the table changes.

```text
Insert a user  → Flow emits updated list
Update a user  → Flow emits updated list
Delete a user  → Flow emits updated list
```

The `suspend` and `Flow` you learned earlier

are the same tools Room uses.

---

## Production Code

A real DAO from an e-commerce application:

```kotlin
@Dao
interface OrderDao {

    @Query("SELECT * FROM orders ORDER BY created_at DESC")
    fun observeOrders(): Flow<List<OrderEntity>>

    @Query("SELECT * FROM orders WHERE order_id = :id")
    suspend fun getOrderById(id: String): OrderEntity?

    @Query("SELECT * FROM orders WHERE status = :status")
    fun observeByStatus(status: String): Flow<List<OrderEntity>>

    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insertAll(orders: List<OrderEntity>)

    @Update
    suspend fun update(order: OrderEntity)

    @Query("DELETE FROM orders WHERE status = 'cancelled'")
    suspend fun clearCancelled()

}
```

### How To Read It

```text
observeOrders()

↓

Returns Flow — The UI Observes This

(New emission on every table change)

---

getOrderById(id)

↓

suspend — One-time read, does not block main thread

Returns nullable — Order may not exist

---

insertAll(orders)

↓

suspend — Writes to database off main thread

OnConflictStrategy.REPLACE — If an order with the same ID exists, replace it

---

clearCancelled()

↓

@Query with DELETE — Custom SQL for bulk operations

suspend — Runs off main thread
```

---

## OnConflictStrategy

When inserting a row whose primary key already exists:

```text
ABORT    → Throw an error (default)

REPLACE  → Delete the old row, insert the new one

IGNORE   → Keep the old row, discard the new one
```

Production code almost always uses REPLACE:

```kotlin
@Insert(onConflict = OnConflictStrategy.REPLACE)
suspend fun insertAll(orders: List<OrderEntity>)
```

This makes "insert or update" a single operation.

---

## Production Notes

```text
* DAOs are interfaces. Room generates the implementation at compile time.

* Use suspend for one-time operations (insert, update, delete, single reads).

* Use Flow for observable queries (the UI should react to changes).

* @Query is the only annotation that requires you to write SQL.

* OnConflictStrategy.REPLACE is the most common conflict strategy in production.

* A DAO should contain only database operations. Business logic belongs in the Repository.
```

---

## Common Misconception

A common misconception is:

```text
DAO = Repository
```

Incorrect.

```text
DAO

↓

Database Operations Only

(Raw data in, raw data out)

---

Repository

↓

Application Data Owner

(Decides: local or remote? Maps Entity to domain model)
```

A DAO knows nothing about the network,

nothing about business rules,

nothing about the UI.

It only knows how to talk to the database.

> **You'll see this in...**
> - **DOC 11 -- Data Architecture**, where the Repository calls DAO methods and coordinates with remote data sources
> - **DOC 12 -- Dependency Management**, where Hilt provides DAO instances by calling abstract methods on the Database class
> - **DOC 16 -- Testing**, where DAOs are tested against an in-memory Room database

---

## A Natural Question

We now know that a DAO can return `Flow<List<OrderEntity>>`.

We learned in DOC 3 that Flow emits values over time.

What does this actually look like when connected to the UI?

When the database changes,

does the UI really update automatically?

That question leads us to:

```text
Room + Flow
```

---

## Revision

### Core Idea

```text
DAO = The Interface That Defines Allowed Database Operations
```

### Mental Model

```text
Application

↓

DAO (Interface)

↓

Room (Generated Implementation)

↓

SQLite
```

### Production Recognition

```kotlin
@Dao
interface OrderDao {
    @Query("...") fun observe(): Flow<...>
    @Insert suspend fun insert(...)
}
```

↓

```text
Database Operations — Suspend For Writes, Flow For Reads
```

### Previous Concept

```text
Entity
```

### Next Concept

```text
Room + Flow
```
