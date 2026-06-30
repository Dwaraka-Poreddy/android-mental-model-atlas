# Room

## Looking Back

In the previous concepts we learned:

```text
Database

↓

Tables, Rows, Columns

↓

SQL

↓

Read And Write Data
```

We know that Android uses SQLite as its built-in database.

We know SQL is the language for querying it.

A natural question appears:

```text
Do Android Developers Write Raw SQL Strings In Their Kotlin Code?
```

---

## The Problem

Imagine writing a query as a raw string.

```kotlin
val cursor = db.rawQuery(
    "SELCT * FROM usrs WHERE id = " + id,
    null
)
```

There are two bugs in that line.

```text
SELCT   → Typo (should be SELECT)

usrs    → Typo (should be users)
```

The code compiles.

No errors appear in the IDE.

The app launches successfully.

Then,

the moment this query runs:

```text
Runtime Crash
```

The problem is clear.

```text
Raw SQL

↓

Strings

↓

No Compile-Time Checking

↓

Bugs Hide Until Runtime
```

---

## It Gets Worse

Beyond typos,

raw SQL has other problems.

```text
Column name changes → Every query must be updated manually

Type mismatches    → String stored where Int expected

Missing columns    → No warning until runtime

Cursor management  → Manual open, read, close, handle errors
```

Every one of these is invisible at compile time.

Every one of these causes a crash at runtime.

---

## What We Actually Want

```text
Describe What We Want In Kotlin

↓

Let The Compiler Verify It

↓

Generate The SQL Automatically
```

We want database operations that look and feel like Kotlin.

We want the compiler to catch our mistakes.

---

## Room

**Room** is Android's recommended database library.

Its responsibility is to provide a Kotlin-first interface to SQLite.

```text
Your Kotlin Code

↓

Room

↓

SQLite
```

Room does not replace SQLite.

SQLite is still the database.

Room is a layer on top that provides:

```text
Compile-Time Verification

↓

Type Safety

↓

Kotlin / Coroutine Integration
```

---

## The Mental Model

Think of Room as a translator.

```text
You Speak Kotlin

↓

Room Translates To SQL

↓

SQLite Executes The Query
```

You describe what you want using Kotlin annotations and interfaces.

Room generates the SQL and manages the database for you.

If you make a mistake,

the compiler catches it before the app ever runs.

---

## The Three Parts

Room has three fundamental components.

```text
Entity

↓

Describes A Table

---

DAO

↓

Describes Allowed Operations

---

Database

↓

Holds Everything Together
```

Every Room database contains at least one Entity and one DAO.

The Database class connects them.

---

## How They Relate

```text
Entity

↓

"This is what a row looks like"

---

DAO

↓

"These are the operations I allow"

---

Database

↓

"Here are the tables and their DAOs"
```

Think of a library.

```text
Entity    = The card catalog format
              (describes what information each book entry contains)

DAO       = The librarian
              (knows how to find, add, and remove books)

Database  = The library building
              (contains the catalog and the librarian)
```

---

## Minimal Code

The smallest possible Room setup:

```kotlin
@Entity
data class User(
    @PrimaryKey val id: Int,
    val name: String
)

@Dao
interface UserDao {
    @Query("SELECT * FROM User")
    fun getAll(): List<User>
}

@Database(entities = [User::class], version = 1)
abstract class AppDatabase : RoomDatabase() {
    abstract fun userDao(): UserDao
}
```

---

## What Room Does At Compile Time

When you build the project,

Room reads your annotations and verifies:

```text
Does The Table Exist?

↓

Do The Columns Match?

↓

Is The SQL Valid?

↓

Do The Return Types Match?
```

If anything is wrong:

```text
Compile Error

↓

You Fix It Before The App Runs
```

Compare this to raw SQL:

```text
Raw SQL     → Crash At Runtime

Room        → Error At Compile Time
```

This single difference prevents entire categories of production bugs.

---

## Production Code

```kotlin
@Entity(tableName = "orders")
data class OrderEntity(
    @PrimaryKey val orderId: String,
    val restaurantName: String,
    val totalAmount: Double,
    val status: String,
    val createdAt: Long
)

@Dao
interface OrderDao {

    @Query("SELECT * FROM orders ORDER BY createdAt DESC")
    fun getOrders(): Flow<List<OrderEntity>>

    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insertAll(orders: List<OrderEntity>)

    @Query("DELETE FROM orders WHERE status = 'delivered'")
    suspend fun clearDelivered()

}

@Database(
    entities = [OrderEntity::class],
    version = 1
)
abstract class AppDatabase : RoomDatabase() {
    abstract fun orderDao(): OrderDao
}
```

### How To Read It

```text
OrderEntity

↓

Describes The "orders" Table

(Each Property = A Column)

---

OrderDao

↓

Defines Allowed Operations

(Query, Insert, Delete)

---

AppDatabase

↓

Connects Entity And DAO

(Single Entry Point)
```

Notice two things from earlier docs:

```text
Flow<List<OrderEntity>>     → DOC 3 (Flow)

suspend fun insertAll(...)  → DOC 2 (Coroutines)
```

Room integrates directly with the reactive patterns you already know.

---

## Production Notes

```text
* Room is a compile-time verified layer over SQLite.

* It does not replace SQLite. It makes SQLite safe to use from Kotlin.

* Every Room database needs at least one Entity, one DAO, and one Database class.

* Room verifies SQL queries at compile time. Invalid queries fail the build.

* Room generates implementation code. You write interfaces and annotations.

* Room is Google's recommended persistence library for Android.
```

---

## Common Misconception

A common misconception is:

```text
Room = A Database
```

Incorrect.

```text
SQLite

↓

The Database (Stores Data On Disk)

---

Room

↓

A Library That Makes SQLite Safe And Kotlin-Friendly
```

Room does not store data.

SQLite stores data.

Room translates between your Kotlin code and SQLite.

---

> **You'll see this in...**
> - **DOC 11 -- Data Architecture**, where Room becomes the local data source inside the Repository pattern
> - **DOC 12 -- Dependency Management**, where Hilt provides the Room Database and DAOs as injectable dependencies
> - **DOC 16 -- Testing**, where Room provides an in-memory database for fast, isolated tests

---

## A Natural Question

Room has three parts: Entity, DAO, and Database.

We listed them,

but we haven't examined any of them closely.

What does an Entity actually look like?

How do you describe a database table using Kotlin?

That question leads us to the next concept:

```text
Entity
```

---

## Revision

### Core Idea

```text
Room = A Kotlin-First Interface To SQLite That Catches Mistakes At Compile Time
```

### Mental Model

```text
Kotlin Code

↓

Room (Compile-Time Verification)

↓

SQLite (Actual Database)
```

### Production Recognition

```text
@Entity + @Dao + @Database

↓

Room — The Three-Part Structure
```

### Previous Concept

```text
SQL Basics
```

### Next Concept

```text
Entity
```
