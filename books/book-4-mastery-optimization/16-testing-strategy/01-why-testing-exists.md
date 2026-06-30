# Why Testing Exists

## Looking Back

We've built a complete production app:

```text
Compose UI (DOC 7)

↓

ViewModels (DOC 5)

↓

Repositories (DOC 13)

↓

Room (DOC 12)

↓

Retrofit (DOC 11)

↓

Hilt (DOC 14)
```

One question remains:

```text
How Do We Know It Works?
```

---

## The Problem

Consider three real situations.

---

**Situation 1:**

```text
"I refactored the UserRepository.

Now three screens are broken.

I didn't know until QA found it."
```

---

**Situation 2:**

```text
"The login flow works on my device.

Three users reported it crashes on older phones.

I can't reproduce it."
```

---

**Situation 3:**

```text
"We spent 2 weeks manually testing

before each release."
```

---

Each of these situations has the same root cause:

```text
No Automated Proof That The Code Works
```

---

## The Mental Model

```text
Tests

=

Automated Proof That Your Code Does What You Think It Does

At The Speed Of Compilation
```

Instead of:

```text
Make Change

↓

Open App

↓

Navigate Manually

↓

Observe

↓

Hope Nothing Else Broke
```

We have:

```text
Make Change

↓

Run Tests

↓

Pass Or Fail

↓

Instant Feedback
```

---

## The Testing Pyramid

Not all tests are equal.

```text
            ┌──────┐
            │  E2E  │  ← slow, few, test full flows
            ├────────┤
            │   UI   │  ← medium, test Compose components
            ├────────────┤
            │ Integration │  ← multiple real layers together
            ├────────────────────┤
            │      Unit Tests      │  ← fast, many, test one thing
            └────────────────────┘
```

The pyramid tells us the ratio:

```text
Many unit tests

Fewer integration tests

Even fewer UI tests

Very few E2E tests
```

A 70/20/10 split (unit/integration/UI) is a common starting point.

---

## What Each Layer Tests

### Unit Tests

```text
One Class

One Function

One Responsibility
```

Run in milliseconds.

No device needed.

Test:

```text
ViewModel logic

Repository decisions

Data mappers
```

---

### Integration Tests

```text
Multiple Real Components Together
```

Test:

```text
Room DAO with a real in-memory database

Retrofit with MockWebServer
```

---

### UI Tests

```text
Composables Rendered

Interactions Verified
```

Test:

```text
Click button

↓

State changes

↓

UI updates
```

---

### E2E Tests

```text
Full App Flow

On Real Device Or Emulator
```

Test:

```text
Login

↓

Home Screen

↓

Order Flow
```

---

## Minimal Code

```kotlin
@Test
fun `user name is displayed when state is success`() {
    // Arrange
    val uiState = HomeUiState.Success(user = User(name = "Dwaraka"))
    // Assert
    assertEquals("Dwaraka", uiState.user.name)
}
```

This is a unit test.

It runs in milliseconds.

It requires no device.

---

## Production Code

```kotlin
class HomeViewModelTest {

    private val fakeRepository = FakeUserRepository()

    private lateinit var viewModel: HomeViewModel

    @Before
    fun setup() {
        viewModel = HomeViewModel(fakeRepository)
    }

    @Test
    fun `given user loads successfully, ui state shows user name`() = runTest {
        fakeRepository.emit(User(id = "1", name = "Dwaraka"))

        val uiState = viewModel.uiState.value

        assertIs<HomeUiState.Success>(uiState)
        assertEquals("Dwaraka", uiState.user.name)
    }

    @Test
    fun `given network fails, ui state shows error`() = runTest {
        fakeRepository.setError(shouldThrow = true)

        viewModel.refresh()

        val uiState = viewModel.uiState.value

        assertIs<HomeUiState.Error>(uiState)
    }
}
```

### How To Read It

```text
FakeUserRepository

↓

A controlled test double

Not a real network or database

We decide what it returns

-------------------------

HomeViewModel(fakeRepository)

↓

Direct constructor injection

No Hilt needed in unit tests

This is WHY constructor injection matters (DOC 14)

-------------------------

runTest { ... }

↓

A coroutine scope designed for testing

Handles suspend functions

-------------------------

assertIs<HomeUiState.Success>(uiState)

↓

Verifies the shape of the output state

Tests behavior, not implementation
```

---

## Production Notes

```text
• Tests are documentation.

  givenUserIsLoggedIn_whenViewModelCreated_thenShowHomeState
  tells you exactly what the code does.

• Tests catch regressions.

  When you change X and Y breaks,
  tests tell you immediately.

• Untested code is tech debt.

  You pay it every refactor,
  every release,
  every debugging session.

• A 70/20/10 split (unit/integration/UI) is a proven starting ratio.

• Tests run in CI.

  Broken tests block merges before they reach production.
```

---

## A Common Misconception

```text
"I don't have time to write tests."
```

Incorrect.

Untested code requires:

```text
Time for manual QA

↓

Time for bug hunting

↓

Time for fearful refactoring
```

Tests pay back within days on any feature over 200 lines.

The question is not:

```text
Do I Have Time To Write Tests?
```

The question is:

```text
Do I Have Time To NOT Write Tests?
```

---

## A Natural Question

We use coroutines everywhere.

Every function that does I/O is a `suspend fun`.

```text
How Do We Test Code That Suspends?
```

That question leads us to the next chapter:

```text
Testing Coroutines
```

---

## Revision

### Core Idea

```text
Tests = Automated Proof That Code Does What You Think It Does
```

### Mental Model

```text
            ┌──────┐
            │  E2E  │
            ├────────┤
            │   UI   │
            ├────────────┤
            │ Integration │
            ├────────────────────┤
            │      Unit Tests      │
            └────────────────────┘
```

### Production Recognition

```text
@Test
fun `given X, when Y, then Z`()

↓

A single behavior verified automatically
```

### Previous Concept

```text
Background Work (DOC 15)
```

### Next Concept

```text
Testing Coroutines
```
