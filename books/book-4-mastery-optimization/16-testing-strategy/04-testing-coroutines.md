# Testing Coroutines

## Looking Back

Every function in our app that does I/O is a `suspend fun` (DOC 2).

```text
suspend fun getUser(): User

suspend fun refreshOrders(): Result<Unit>

suspend fun saveProfile(profile: Profile)
```

A natural question appears:

```text
How Do We Test Code That Suspends?
```

---

## The Problem

Consider a regular JUnit test:

```kotlin
@Test
fun `user repository returns user when fetch succeeds`() {
    val result = userRepository.getUser("123")
    assertEquals("Dwaraka", result.name)
}
```

This fails to compile.

```text
Suspend function 'getUser' should be called only from

a coroutine or another suspend function.
```

Regular JUnit tests run synchronously.

`suspend fun` needs a coroutine scope.

```text
No Coroutine Scope

↓

Cannot Call Suspend Functions

↓

Cannot Test Our Code
```

---

## The Mental Model

```text
runTest

=

A Coroutine Scope Designed For Testing

It Runs The Coroutine

Advances Time Virtually

Returns When The Coroutine Completes
```

---

## The Key Tool: `runTest`

```kotlin
@Test
fun `user repository returns user when fetch succeeds`() = runTest {
    val result = userRepository.getUser("123")
    assertEquals("Dwaraka", result.name)
}
```

Now we're inside a coroutine scope.

`suspend fun` calls work.

`delay()` completes instantly.

---

## TestDispatcher — Controlling Time

In a real app:

```text
Dispatchers.IO

↓

Runs on a thread pool

Timing is real

Tests would be slow and flaky
```

In tests:

```text
StandardTestDispatcher

↓

You control when coroutines run

advanceUntilIdle() lets them complete

-------------------------

UnconfinedTestDispatcher

↓

Coroutines run eagerly

Like synchronous code

Simpler for most tests
```

---

## The MainDispatcherRule

ViewModels use `viewModelScope`,

which internally uses `Dispatchers.Main`.

`Dispatchers.Main` doesn't exist on the JVM.

Without a replacement, ViewModel tests throw:

```text
Module with the Main dispatcher had failed to initialize.
```

The fix is a `TestRule` you write once and reuse:

```kotlin
class MainDispatcherRule(
    val testDispatcher: TestCoroutineDispatcher =
        UnconfinedTestDispatcher()
) : TestWatcher() {

    override fun starting(description: Description) {
        Dispatchers.setMain(testDispatcher)
    }

    override fun finished(description: Description) {
        Dispatchers.resetMain()
    }
}
```

---

## Minimal Code

```kotlin
@Test
fun `loads user successfully`() = runTest {
    val result = fakeRepository.getUser("1")
    assertEquals("Dwaraka", result.name)
}
```

One annotation.

One scope.

Suspend functions work.

---

## Production Code

```kotlin
class HomeViewModelTest {

    @get:Rule
    val mainDispatcherRule = MainDispatcherRule()

    private val fakeUserRepository = FakeUserRepository()
    private lateinit var viewModel: HomeViewModel

    @Before
    fun setup() {
        viewModel = HomeViewModel(fakeUserRepository)
    }

    @Test
    fun `when user loads successfully, ui state shows user name`() = runTest {
        fakeUserRepository.emit(User(id = "1", name = "Dwaraka"))

        val uiState = viewModel.uiState.value

        assertIs<HomeUiState.Success>(uiState)
        assertEquals("Dwaraka", uiState.user.name)
    }

    @Test
    fun `when refresh is called, loading state appears first`() = runTest {
        val states = mutableListOf<HomeUiState>()

        val job = launch {
            viewModel.uiState.collect { states.add(it) }
        }

        viewModel.refresh()
        advanceUntilIdle()

        job.cancel()

        assertTrue(states.any { it is HomeUiState.Loading })
    }
}
```

### How To Read It

```text
@get:Rule
val mainDispatcherRule = MainDispatcherRule()

↓

Replaces Dispatchers.Main with TestDispatcher

Without this, viewModelScope.launch { } throws on JVM

-------------------------

FakeUserRepository

↓

A test double we control (covered in the next chapter)

We tell it what to emit

No real network, no real database

-------------------------

viewModel = HomeViewModel(fakeUserRepository)

↓

We inject the fake directly

No Hilt needed in unit tests

This is WHY we use constructor injection (DOC 14)

-------------------------

runTest { ... }

↓

Coroutine scope for tests

Time is virtual

delay(1000) takes 0ms

-------------------------

advanceUntilIdle()

↓

Runs all pending coroutines to completion

Used when you want to observe intermediate states
```

---

## Testing Flow Emissions Over Time

Sometimes you need to assert multiple emissions from a Flow:

```kotlin
@Test
fun `flow emits loading then success`() = runTest {
    viewModel.uiState.test {
        assertEquals(HomeUiState.Loading, awaitItem())
        assertIs<HomeUiState.Success>(awaitItem())
        cancelAndIgnoreRemainingEvents()
    }
}
```

The `test { }` block comes from the **Turbine** library.

It turns Flow testing into a sequential assertion list.

---

## Production Notes

```text
• runTest comes from kotlinx-coroutines-test.

  Add it to your test dependencies:
  testImplementation("org.jetbrains.kotlinx:kotlinx-coroutines-test")

• Write MainDispatcherRule once, reuse across all ViewModel tests.

• UnconfinedTestDispatcher is simpler for most tests —
  coroutines run eagerly without needing advanceUntilIdle().

• Use Turbine for testing Flow emissions over time.
  It makes sequential assertions readable.

• delay() inside runTest completes instantly.
  Virtual time means slow network calls become instant in tests.
```

---

## A Common Misconception

```text
"I need to use Thread.sleep() to wait for coroutines in tests."
```

Incorrect.

```text
Thread.sleep()

↓

Real time passes

Tests become slow and flaky

-------------------------

advanceUntilIdle()

↓

Virtual time advances

All pending coroutines complete

Tests remain fast
```

---

## A Natural Question

We used `FakeUserRepository` in the test.

```text
What Exactly Is A Fake?

How Is It Different From A Mock?

When Should We Use Each?
```

That question leads us to the next chapter:

```text
Fake vs Mock
```

---

## Revision

### Core Idea

```text
runTest = A Coroutine Scope For Testing Suspend Functions
```

### Mental Model

```text
Real App

↓

Dispatchers.IO / Dispatchers.Main

↓

Real Time

↓

Real Thread Pool

-------------------------

Tests

↓

TestDispatcher

↓

Virtual Time

↓

Instant, Deterministic
```

### Production Recognition

```kotlin
@Test
fun `...`() = runTest {
    // suspend functions work here
}
```

### Previous Concept

```text
Why Testing Exists
```

### Next Concept

```text
Fake vs Mock
```
