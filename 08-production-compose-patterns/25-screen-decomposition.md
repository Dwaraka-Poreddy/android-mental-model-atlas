# Screen Decomposition

## Observation

Imagine we're building a Home screen.

Initially,

it's very simple.

```text
Toolbar

↓

Balance

↓

Quick Actions
```

Everything easily fits inside one composable.

```kotlin
@Composable
fun HomeScreen() {

    Toolbar()

    BalanceCard()

    QuickActions()

}
```

This is perfectly reasonable.

---

## A Few Months Later

The product grows.

The screen now contains:

```text
Toolbar

Balance

Stories

Promotions

Recent Transactions

Recommendations

Quick Actions

Loading State

Empty State

Error State

Bottom Sheet
```

The composable keeps growing.

Soon,

it looks something like this.

```kotlin
@Composable
fun HomeScreen() {

    ...

    // 700+ lines

    ...

}
```

The application still works.

A natural question appears.

> **If the screen works perfectly, why should we split it?**

---

## The Common Answer

Many tutorials answer:

> **"Because smaller functions are cleaner."**

That's true.

But it isn't the real reason.

The biggest benefit isn't cleaner code.

It's reducing complexity.

---

## The Real Problem

Imagine your product manager says:

> **"Update the Recent Transactions section."**

Where would you rather work?

Inside:

```text
HomeScreen.kt

900 lines
```

Or inside:

```text
RecentTransactionsSection.kt

80 lines
```

The second option is dramatically easier.

Notice something.

We didn't improve the runtime.

We improved the developer's ability to understand the code.

---

## One Screen, Many Responsibilities

Let's look at our large composable again.

```text
HomeScreen

↓

Toolbar

Balance

Stories

Promotions

Transactions

Recommendations

Bottom Sheet

Loading

Error

Empty
```

`HomeScreen()` is responsible for everything.

That's a lot of responsibility for a single function.

Now imagine we reorganize it.

```text
HomeScreen

├── ToolbarSection()

├── BalanceSection()

├── StoriesSection()

├── PromotionsSection()

├── RecentTransactionsSection()

├── RecommendationsSection()

├── BottomSheet()
```

The screen hasn't changed.

Only the organization has.

Each composable now represents one meaningful part of the UI.

---

## Single Responsibility Returns

Earlier in the Atlas,

we learned about the **Single Responsibility Principle**.

That idea appears again here.

Not for classes.

For composables.

A composable should ideally have one clear responsibility.

For example,

instead of:

```text
HomeScreen()
```

doing everything,

we now have:

```text
BalanceSection()
```

whose responsibility is simply:

> **Display the user's balance.**

Smaller responsibilities make code easier to understand,

modify,

and test.

---

## Decomposition Is Not About Performance

This is an important misconception.

Many developers believe:

```text
Smaller Composables

↓

Better Performance
```

Not necessarily.

Compose already has sophisticated recomposition scopes.

Breaking a screen into many composables doesn't automatically make it faster.

The primary goal of decomposition is **maintainability**,

not runtime optimization.

Performance is a separate concern,

which we already explored in Part 6.

---

## Better Navigation

Imagine opening a file containing:

```text
HomeScreen()

↓

900 lines
```

Finding a specific feature can take time.

Now imagine opening:

```text
HomeScreen()

├── ToolbarSection()

├── BalanceSection()

├── PromotionsSection()

├── TransactionsSection()

├── RecommendationsSection()
```

The structure itself explains the screen.

You spend less time searching,

and more time solving the actual problem.

Good architecture reduces cognitive load.

---

## Better Testing

Suppose QA reports:

> **"The Recent Transactions section isn't rendering correctly."**

With a massive screen,

you begin searching through hundreds of lines.

With decomposition,

you immediately know where to look.

```text
RecentTransactionsSection.kt
```

Smaller composables isolate behavior.

That makes debugging much easier.

---

## Better Team Collaboration

Imagine four engineers working on the same screen.

Without decomposition,

everyone edits:

```text
HomeScreen.kt
```

Merge conflicts become common.

Now imagine the screen is decomposed.

```text
ToolbarSection.kt

BalanceSection.kt

StoriesSection.kt

RecentTransactionsSection.kt
```

Each engineer can work independently.

Architecture doesn't just help the code.

It helps the team.

---

## How Small Is Too Small?

A common mistake is taking decomposition too far.

Imagine this.

```kotlin
TitleText()

Spacer16()

BalanceLabel()

BalanceValue()

DividerLine()
```

Technically,

these are reusable composables.

But together,

they don't represent meaningful concepts.

This is fragmentation,

not decomposition.

A good composable should represent something that makes sense from the product's perspective.

Examples include:

- Balance Card
- User Profile
- Transaction Row
- Promotions Section

These are concepts users recognize,

not individual UI widgets.

---

## Thinking Like A Production Engineer

Whenever a composable starts growing,

don't ask:

> **"How many lines does this function have?"**

Instead ask:

> **"How many responsibilities does this composable have?"**

A long composable isn't necessarily bad.

A composable with many unrelated responsibilities usually is.

Focus on responsibility,

not line count.

---

## Beyond Compose

This idea isn't unique to Compose.

You'll see the same principle throughout software engineering.

- Classes are decomposed into smaller classes.
- Functions are decomposed into smaller functions.
- Modules are decomposed into smaller modules.
- Services are decomposed into smaller services.

Compose simply applies the same architectural principle to UI.

Large systems remain maintainable because they're divided into understandable pieces.

---

## Bringing Everything Together

```text
Large Screen

↓

Many Responsibilities

↓

Split By Responsibility

↓

Smaller Composables

↓

Easier To Understand

Easier To Test

Easier To Modify

Easier To Scale
```

Notice what improved.

Not the runtime.

The developer experience.

---

## Revision

### Core Idea

Screen decomposition divides a large screen into smaller composables,

where each composable has a clear,

focused responsibility.

The goal is maintainability,

not performance.

---

### Engineering Mindset

Don't think:

> **"How can I make this composable smaller?"**

Think:

> **"How can I give this composable a single, clear responsibility?"**

That's the real objective.

---

### Key Insight

Compose architecture isn't about splitting code into tiny functions.

It's about organizing the UI into meaningful,

independent concepts that humans can understand and evolve over time.

---

## One Remaining Question

We've learned how to decompose an entire screen.

A natural question appears.

> **Once we've broken a screen into smaller composables, how do we decide which of those composables should be reused across multiple screens and which should remain screen-specific?**

That leads us to the next concept.

**Reusable UI Components**
