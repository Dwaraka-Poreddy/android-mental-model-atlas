# Entity

## Looking Back

In the previous concept we learned that Room has three parts:

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

Entity is the first.

A natural question appears:

```text
How Do You Describe A Database Table Using Kotlin?
```

---

## The Problem

We know from Database Fundamentals that a table looks like this:

```text
┌────────┬──────────────┬─────────────────────┐
│ id     │ name         │ email               │
├────────┼──────────────┼─────────────────────┤
│ 1      │ Dwaraka      │ dwaraka@example.com │
│ 2      │ Priya        │ priya@example.com   │
│ 3      │ Rahul        │ rahul@example.com   │
└────────┴──────────────┴─────────────────────┘
```

In raw SQL,

you would describe this table as:

```sql
CREATE TABLE users (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    email TEXT
);
```

But we are writing Kotlin,

not SQL.

We need a way to express this table structure in Kotlin

and let Room generate the SQL.

---

## The Mental Model

An Entity is a data class that maps to a database table.

```text
Data Class

↓

@Entity Annotation

↓

Room Generates A Table
```

Each property of the data class becomes a column.

```text
Property = Column

Instance = Row
```

This is the core idea.

If you understand this mapping,

you understand Entity.

---

## Minimal Code

```kotlin
@Entity
data class User(
    @PrimaryKey val id: Int,
    val name: String
)
```

That is a complete Entity.

Room reads it and generates:

```text
Table: User

Columns: id (INTEGER), name (TEXT)

Primary Key: id
```

---

## Reading The Annotations

### @Entity

```text
@Entity

↓

"This Data Class Represents A Database Table"
```

Without this annotation,

it is just a regular data class.

With it,

Room knows to create a table.

---

### @PrimaryKey

```text
@PrimaryKey

↓

"This Column Uniquely Identifies Each Row"
```

Every table needs a primary key.

It prevents duplicate rows.

```text
id = 1  → One and only one row
id = 2  → One and only one row
id = 1  → Rejected (already exists)
```

---

### @ColumnInfo

```text
@ColumnInfo

↓

"Customize The Column Name Or Type"
```

By default,

Room uses the property name as the column name.

If you need a different name:

```kotlin
@ColumnInfo(name = "created_at")
val createdAt: Long
```

```text
Kotlin Property: createdAt

Database Column: created_at
```

This is useful when the database uses snake_case

but your Kotlin code uses camelCase.

---

## The Mapping Visualized

```kotlin
@Entity(tableName = "users")
data class UserEntity(
    @PrimaryKey val id: Int,
    val name: String,
    val email: String?
)
```

Becomes:

```text
Table: users

┌────────────┬────────────┬────────────┐
│ id         │ name       │ email      │
│ INTEGER    │ TEXT       │ TEXT       │
│ NOT NULL   │ NOT NULL   │ NULLABLE   │
│ PRIMARY KEY│            │            │
└────────────┴────────────┴────────────┘
```

Notice:

```text
String   → NOT NULL
String?  → NULLABLE
```

Kotlin's null safety maps directly to SQL's NULL constraints.

This is one of Room's most elegant features.

---

## Auto-Generated Primary Keys

Sometimes you don't want to assign IDs manually.

```kotlin
@Entity
data class Message(
    @PrimaryKey(autoGenerate = true)
    val id: Int = 0,
    val text: String
)
```

```text
autoGenerate = true

↓

Room (via SQLite) Assigns IDs Automatically

↓

1, 2, 3, 4, 5 ...
```

You insert a row without providing an ID.

SQLite assigns the next available number.

---

## Production Code

A real Entity from an e-commerce application:

```kotlin
@Entity(
    tableName = "users",
    indices = [Index(value = ["email"], unique = true)]
)
data class UserEntity(

    @PrimaryKey
    val userId: String,

    val displayName: String,

    val email: String,

    val avatarUrl: String?,

    @ColumnInfo(name = "created_at")
    val createdAt: Long,

    @ColumnInfo(name = "last_login_at")
    val lastLoginAt: Long?,

    @ColumnInfo(name = "is_verified")
    val isVerified: Boolean

)
```

### How To Read It

```text
UserEntity

↓

Maps To "users" Table

---

userId (String, NOT NULL)

↓

Primary Key — Uniquely Identifies Each User

---

email (String, NOT NULL, UNIQUE INDEX)

↓

No Two Users Can Share The Same Email

---

avatarUrl (String?)

↓

Nullable — Not Every User Has An Avatar

---

createdAt, lastLoginAt

↓

Timestamps — Column Names Use snake_case

---

isVerified (Boolean)

↓

Maps To INTEGER In SQLite (0 = false, 1 = true)
```

---

## Entity vs Domain Model

A common question is why production apps use "UserEntity" instead of just "User."

```text
UserEntity

↓

Database Representation

(Matches Table Schema)

---

User

↓

Domain Model

(What The Rest Of The App Uses)
```

The Entity may have:

```text
created_at (Long — timestamp in milliseconds)
```

The domain model may have:

```text
createdAt (Instant — proper date type)
```

Keeping them separate prevents database details from leaking into the rest of the application.

---

## Production Notes

```text
* Name Entities with a suffix: UserEntity, OrderEntity, MessageEntity.

* Use @ColumnInfo for snake_case column names. Kotlin properties stay camelCase.

* Nullable types (String?) map to nullable columns. Non-null types (String) map to NOT NULL columns.

* Use @PrimaryKey(autoGenerate = true) when you don't control the ID (local-only data).

* Use indices for columns you frequently query or filter by.

* Keep Entities separate from domain models. Map between them in the Repository.
```

---

## Common Misconception

A common misconception is:

```text
Entity = The Model Used Everywhere In The App
```

Incorrect.

```text
Entity

↓

Database Representation Only

---

Domain Model

↓

Used By ViewModel, UI, Business Logic
```

The Entity matches the database schema.

The domain model matches what the rest of the application needs.

The Repository converts between them.

---

> **You'll see this in...**
> - **DOC 11 -- Data Architecture**, where Entities are mapped to domain models inside the Repository
> - **DOC 12 -- Dependency Management**, where Hilt provides the database that contains these Entities

---

## A Natural Question

We now know how to describe a table using a data class.

But a table alone does nothing.

We need to read from it.

We need to write to it.

How do we define the operations allowed on this table?

That question leads us to the next concept:

```text
DAO
```

---

## Revision

### Core Idea

```text
Entity = A Data Class That Maps To A Database Table
```

### Mental Model

```text
@Entity Data Class

↓

Property = Column

↓

Instance = Row
```

### Production Recognition

```kotlin
@Entity(tableName = "users")
data class UserEntity(
    @PrimaryKey val userId: String,
    ...
)
```

↓

```text
A Database Table Described In Kotlin
```

### Previous Concept

```text
Room
```

### Next Concept

```text
DAO
```
