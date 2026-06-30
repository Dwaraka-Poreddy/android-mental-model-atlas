# Testing Room

## Looking Back

We can unit test ViewModels with Fakes.

```text
ViewModel

↓

FakeUserRepository

↓

No real database, no real network
```

But who tests the DAO itself?

Room generates the implementation code for our queries.

```kotlin
@Query("SELECT * FROM orders WHERE status = :status ORDER BY created_at DESC")
fun observeByStatus(status: String): Flow<List<OrderEntity>>
```

Room checks the syntax at compile time.

If the column name is wrong — `created_add` instead of `created_at` — the build fails.

But if the query logic is wrong:

```text
Wrong ORDER BY direction

Missing WHERE clause

Incorrect JOIN condition
```

Only a test will catch it.

---

## The Problem

We cannot unit test a DAO with a Fake.

The DAO is the thing we're testing.

```text
Real network → use Fake

Real database → use ??? 
```

We need a real Room database in our tests.

But a real database:

```text
Writes to disk

Persists between tests

Tests interfere with each other

Tests are slow
```

There must be a better way.

---

## The Mental Model

```text
In-Memory Database

=

A Real Room Database

That Lives Only In Memory During The Test

No File On Disk

Resets After Each Test
```

Same Room.

Same DAO.

Same SQL.

No file system.

No test pollution.

---

## The In-Memory Database

```kotlin
val db = Room.inMemoryDatabaseBuilder(
    context,
    AppDatabase::class.java
).build()

val dao = db.orderDao()
```

One line change from the production database:

```text
Room.databaseBuilder(...)    ← production, file on disk

Room.inMemoryDatabaseBuilder(...)    ← tests, memory only
```

Everything else — entities, DAOs, SQL — is identical.

---

## Minimal Code

```kotlin
@Test
fun `insert order and retrieve it`() = runTest {
    val order = OrderEntity(id = "1", status = "pending")
    dao.insert(order)
    val result = dao.getById("1")
    assertEquals("pending", result?.status)
}
```

Real Room, real SQL, no file on disk.

---

## Production Code

```kotlin
@RunWith(AndroidJUnit4::class)
class OrderDaoTest {

    private lateinit var db: AppDatabase
    private lateinit var dao: OrderDao

    @Before
    fun setup() {
        db = Room.inMemoryDatabaseBuilder(
            ApplicationProvider.getApplicationContext(),
            AppDatabase::class.java
        ).build()
        dao = db.orderDao()
    }

    @After
    fun teardown() {
        db.close()
    }

    @Test
    fun `insert orders and observe by status returns correct orders`() = runTest {
        val pending = OrderEntity(id = "1", status = "pending", createdAt = 1000L)
        val delivered = OrderEntity(id = "2", status = "delivered", createdAt = 2000L)

        dao.insertAll(listOf(pending, delivered))

        val result = dao.observeByStatus("pending").first()

        assertEquals(1, result.size)
        assertEquals("1", result[0].id)
    }

    @Test
    fun `orders are returned newest first`() = runTest {
        val older = OrderEntity(id = "1", status = "pending", createdAt = 1000L)
        val newer = OrderEntity(id = "2", status = "pending", createdAt = 9000L)

        dao.insertAll(listOf(older, newer))

        val result = dao.observeByStatus("pending").first()

        assertEquals("2", result[0].id)
    }

    @Test
    fun `empty table returns empty list`() = runTest {
        val result = dao.observeByStatus("pending").first()
        assertTrue(result.isEmpty())
    }
}
```

### How To Read It

```text
@RunWith(AndroidJUnit4::class)

↓

Room needs an Android context even in tests

This runner provides it

Tests run on device, emulator, or with Robolectric

-------------------------

Room.inMemoryDatabaseBuilder(...)

↓

Real Room, real SQL, real @Query execution

No file on disk

When db.close() is called, all data is gone

-------------------------

@Before / @After

↓

Fresh database for every test

Tests cannot affect each other

-------------------------

dao.observeByStatus("pending").first()

↓

.first() collects the first emission from the Flow

Used for one-shot reads in tests

Requires runTest because it suspends

-------------------------

assertEquals(1, result.size)

↓

Verifies the WHERE clause filters correctly

If the query returned both orders, this test would fail

-------------------------

assertEquals("2", result[0].id)

↓

Verifies ORDER BY works correctly

The newest order must appear first
```

---

## What To Test In A DAO

```text
Basic CRUD

↓

Insert, update, delete, retrieve

Does the data round-trip correctly?

-------------------------

Query Filters

↓

WHERE clauses, IN, LIKE

Do they include the right rows and exclude the wrong ones?

-------------------------

Sort Order

↓

ORDER BY direction

Are results ordered as expected?

-------------------------

Edge Cases

↓

Empty table

Duplicate primary keys

Null values in optional columns

Maximum row counts
```

---

## Running Without A Device: Robolectric

DAO tests require Android context.

Two options:

```text
Option 1: Run on device or emulator

↓

Accurate, slower, requires Android

-------------------------

Option 2: Robolectric

↓

Simulates Android on the JVM

Faster, no device needed

Add: testImplementation("org.robolectric:robolectric")
     @Config(sdk = [33])
```

Most teams run DAO tests on device in CI.

Robolectric speeds up local development.

---

## Production Notes

```text
• DAO tests are the one place where you use a real database.

  Do not use a Fake — the DAO is what you're testing.

• In-memory databases reset after each test.

  Call db.close() in @After to release resources.

• Test your @Query SQL — Room verifies syntax at compile time,
  but not logic.

• Test boundary conditions:
  empty table, duplicate keys, null values.

• Robolectric allows DAO tests to run on the JVM.
  No emulator needed during local development.
```

---

## A Common Misconception

```text
"Room generates the implementation, so I don't need to test it."
```

Room generates the boilerplate.

You write the query.

```kotlin
@Query("""
    SELECT * FROM orders
    WHERE user_id = :userId
    AND status IN ('pending', 'processing')
    ORDER BY priority DESC, created_at ASC
""")
fun observeActiveOrders(userId: String): Flow<List<OrderEntity>>
```

Room compiles this query successfully.

But:

```text
Is the IN clause correct?

Is the priority sort direction right?

Does this return orders for the correct user?
```

Only a test proves it.

---

## A Natural Question

We can test ViewModels with Fakes.

We can test DAOs with in-memory Room.

```text
What About The UI Itself?

We Built Complex Compose Screens (DOC 7)

How Do We Test Composables?
```

That question leads us to the next chapter:

```text
Testing Compose
```

---

## Revision

### Core Idea

```text
In-Memory Room = Real Room Database Without File I/O

Same SQL, Same Queries, Resets After Each Test
```

### Mental Model

```text
Room.inMemoryDatabaseBuilder(context, AppDatabase::class.java)

↓

Real Room

Real SQL

Real @Query Execution

No File On Disk

Resets On db.close()
```

### Production Recognition

```kotlin
@RunWith(AndroidJUnit4::class)
class OrderDaoTest {

    @Before
    fun setup() {
        db = Room.inMemoryDatabaseBuilder(...).build()
    }

    @Test
    fun `...`() = runTest {
        dao.insert(entity)
        val result = dao.query().first()
        assertEquals(expected, result)
    }
}
```

### Previous Concept

```text
Fake vs Mock
```

### Next Concept

```text
Testing Compose
```
