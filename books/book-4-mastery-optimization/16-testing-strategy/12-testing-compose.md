# Testing Compose

## Looking Back

We know how to test:

```text
ViewModels

↓

With Fakes and runTest

-------------------------

DAOs

↓

With In-Memory Room
```

A natural question appears:

```text
What About The Compose UI Itself?
```

---

## The Problem

Consider a Composable with conditional rendering:

```kotlin
@Composable
fun OrderCard(
    order: Order,
    onCancelClick: () -> Unit
) {
    if (order.isCancellable) {
        Button(onClick = onCancelClick) { Text("Cancel") }
    }
    Text(text = order.status)
}
```

Manual testing looks like:

```text
Open the app

↓

Navigate to orders

↓

Find a cancellable order

↓

Verify the button appears

↓

Find a non-cancellable order

↓

Verify the button is absent

↓

Do this for every state change

↓

Do this for every release
```

There must be a better way.

---

## The Mental Model

```text
ComposeTestRule

=

A Test Harness That Renders Compose UI Without A Real Device

You Can Find Elements

Click Them

Assert What Is Visible
```

---

## The Test Rule

```kotlin
@get:Rule
val composeTestRule = createComposeRule()
```

One line.

No Activity.

No ViewModel.

Just the UI.

---

## Minimal Code

```kotlin
@Test
fun `cancel button visible for cancellable order`() {
    val order = Order(id = "1", status = "pending", isCancellable = true)

    composeTestRule.setContent {
        OrderCard(order = order, onCancelClick = {})
    }

    composeTestRule
        .onNodeWithText("Cancel")
        .assertIsDisplayed()
}
```

Set the content.

Find a node.

Assert on it.

---

## Production Code

```kotlin
class HomeScreenTest {

    @get:Rule
    val composeTestRule = createComposeRule()

    @Test
    fun `loading state shows progress indicator`() {
        composeTestRule.setContent {
            HomeScreen(uiState = HomeUiState.Loading)
        }

        composeTestRule
            .onNodeWithContentDescription("Loading")
            .assertIsDisplayed()
    }

    @Test
    fun `success state shows order list`() {
        val orders = listOf(Order(id = "1", name = "Biryani"))

        composeTestRule.setContent {
            HomeScreen(uiState = HomeUiState.Success(orders))
        }

        composeTestRule
            .onNodeWithText("Biryani")
            .assertIsDisplayed()
    }

    @Test
    fun `error state shows error message`() {
        composeTestRule.setContent {
            HomeScreen(uiState = HomeUiState.Error("Something went wrong"))
        }

        composeTestRule
            .onNodeWithText("Something went wrong")
            .assertIsDisplayed()
    }

    @Test
    fun `clicking cancel calls onCancelOrder with order id`() {
        var cancelledId: String? = null
        val order = Order(id = "1", name = "Biryani", isCancellable = true)

        composeTestRule.setContent {
            HomeScreen(
                uiState = HomeUiState.Success(listOf(order)),
                onCancelOrder = { id -> cancelledId = id }
            )
        }

        composeTestRule.onNodeWithText("Cancel").performClick()

        assertEquals("1", cancelledId)
    }
}
```

### How To Read It

```text
composeTestRule.setContent { HomeScreen(...) }

↓

Renders the Composable in a test environment

No Activity, no ViewModel

We pass state and callbacks directly

-------------------------

HomeUiState.Loading / Success / Error

↓

We test each state independently

One test = one scenario

-------------------------

onNodeWithText("Cancel")

↓

Finds a node in the composition tree with this text

If it doesn't exist, the test fails

-------------------------

onNodeWithContentDescription("Loading")

↓

Finds a node by its accessibility description

Preferred for icons and progress indicators

Screen readers use content descriptions too

-------------------------

performClick()

↓

Simulates a user tap

Triggers the composable's onClick lambda

-------------------------

assertEquals("1", cancelledId)

↓

Verifies the callback was called with the correct argument

Tests behavior, not implementation
```

---

## Finding Nodes

```text
onNodeWithText("Cancel")

↓

Finds by displayed text

For buttons, labels, headings

-------------------------

onNodeWithContentDescription("Loading")

↓

Finds by content description

For icons, images, progress indicators

-------------------------

onNodeWithTag("order_list")

↓

Finds by testTag modifier

For elements without text or description

val modifier = Modifier.testTag("order_list")
```

---

## Why State Hoisting Makes This Possible

This testing approach works because of State Hoisting (DOC 7):

```text
Without State Hoisting

↓

HomeScreen creates its own ViewModel

ViewModel makes real network calls

Tests require real data, real network

Tests are slow and brittle

-------------------------

With State Hoisting

↓

HomeScreen receives state as a parameter

Tests pass state directly

No ViewModel, no network, no database
```

State Hoisting is not just a Compose pattern.

It is what makes Compose testable.

---

## Production Notes

```text
• Test Composables without ViewModels.

  Pass state and callbacks directly.
  This is WHY State Hoisting matters (DOC 7).

• onNodeWithContentDescription is preferred for icons.

  Screen readers use it too.
  Tests and accessibility benefit from the same attribute.

• onNodeWithTag for elements without text or description.

  Add Modifier.testTag("my_tag") to the Composable.
  Use consistent naming conventions.

• Compose UI tests run on device or emulator.

  They are slower than unit tests.
  Use the testing pyramid: fewer UI tests, many unit tests.

• Test each UiState variant separately.

  One test per scenario keeps failures easy to diagnose.
```

---

## A Common Misconception

```text
"I need to test with a real ViewModel to test the UI."
```

Incorrect.

```text
ViewModel Test

↓

Tests that the ViewModel produces the correct state

from the given inputs

-------------------------

Composable Test

↓

Tests that the Composable renders the correct UI

from the given state

-------------------------

These are separate responsibilities

Test them separately
```

A ViewModel test and a Composable test together give full coverage.

Neither requires the other.

---

> **You'll see this in...**
>
> **DOC 17 — Performance & Optimization**, where screenshot testing
> and baseline profiles connect to the test infrastructure
> you build here.

---

## A Natural Question

We've learned to test:

```text
ViewModels (runTest + Fakes)

DAOs (In-Memory Room)

Composables (ComposeTestRule)
```

These are all fast, isolated tests.

```text
Who Tests That Everything Works Together?

End-To-End?
```

That question leads to:

```text
Integration and E2E Testing

(See: Why Testing Exists — Testing Pyramid)
```

---

## Revision

### Core Idea

```text
ComposeTestRule = Test Harness For Compose UI Without A Real Device
```

### Mental Model

```text
composeTestRule.setContent { MyComposable(state = ...) }

↓

Render

↓

onNodeWithText("Cancel")

↓

Find

↓

assertIsDisplayed() / performClick()

↓

Assert Or Act
```

### Production Recognition

```kotlin
@get:Rule
val composeTestRule = createComposeRule()

@Test
fun `...`() {
    composeTestRule.setContent { ... }
    composeTestRule.onNodeWithText("...").assertIsDisplayed()
}
```

### Previous Concept

```text
Testing Room
```

### Next Concept

```text
DOC 17 — Performance & Optimization
```
