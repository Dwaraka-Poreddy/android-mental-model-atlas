# Compose Recomposition Metrics

## Looking Back

In DOC 8 we learned stability —
how Compose decides whether to skip or recompose a Composable.

```text
Stable type
    ↓
Compose trusts the comparison
    ↓
Safe to skip if inputs haven't changed


Unstable type
    ↓
Compose cannot trust the comparison
    ↓
Play it safe — recompose
```

We know the mechanism.

Now we need to see it in action.

```text
Knowing stability exists
    ≠
Knowing which Composables are actually recomposing.
```

A natural question appears:

```text
Our UI feels janky.
We suspect unnecessary recompositions.
How do we find them?
```

---

## The Problem

Here is a component from a food delivery app:

```kotlin
@Composable
fun OrderList(orders: List<Order>, onOrderClick: (Order) -> Unit) {
    orders.forEach { order ->
        OrderCard(order = order, onClick = { onOrderClick(order) })
    }
}
```

This looks correct.

But if `OrderList` recomposes because its parent recomposes —
every `OrderCard` recomposes too,
even if `order` didn't change.

```text
Parent recomposes (unrelated state change)
    ↓
OrderList recomposes
    ↓
Every OrderCard recomposes
    ↓
10, 20, 50 cards — all recomputed
    ↓
UI drops frames
    ↓
User sees jank
```

How do you find this?

You can't see it in the code.

The code looks reasonable.

You need tooling.

---

## Three Tools for Recomposition Analysis

There are three tools, from fastest to most precise.

Use them in order.

```text
1. Layout Inspector (visual, immediate)
    ↓
2. Compose Compiler Metrics (why a composable can't skip)
    ↓
3. Composition Tracing (timeline-level precision)
```

---

## Tool 1 — Layout Inspector

Android Studio provides recomposition counts visually.

```text
View → Tool Windows → Layout Inspector
    ↓
Enable "Show recomposition counts"
    ↓
Numbers appear on each Composable in the tree
    ↓
The number = how many times it recomposed this session
```

What you're looking for:

```text
SimpleCard: 3          ← reasonable for 3 state changes

OrderCard (row 1): 847 ← this is wrong.
OrderCard (row 2): 847 ← scrolling triggered 847 recompositions?
OrderCard (row 3): 847 ← something is recomposing constantly.
```

A number in the hundreds for a simple card
means something is wrong.

---

## Tool 2 — Compose Compiler Metrics

The Compose compiler can emit a report
showing every Composable's stability status.

```kotlin
// In build.gradle.kts (app module)
composeCompiler {
    reportsDestination = layout.buildDirectory.dir("compose_compiler")
    metricsDestination = layout.buildDirectory.dir("compose_compiler")
}
```

Run:

```text
./gradlew assembleDebug
```

Look in `build/compose_compiler/` for a file named
`*-composables.txt`.

A skippable Composable looks like this:

```text
restartable skippable scheme(...) fun OrderCard(
    stable order: Order
    stable onClick: Function0<Unit>
)
```

`skippable` means Compose will skip this
if its inputs haven't changed. Good.

A non-skippable Composable looks like this:

```text
restartable scheme(...) fun OrderCard(
    unstable order: Order
    ...
)
```

No `skippable`.

Compose must recompose this every time,
because it cannot trust the comparison of `order`.

This is why `OrderCard` recomposed 847 times.

---

## Tool 3 — Composition Tracing

For frame-level precision,
add trace sections to your Composables:

```kotlin
Trace.beginSection("OrderCard:render")
// ... composable body
Trace.endSection()
```

These appear in Perfetto traces —
you can see exactly when recompositions happen on the timeline.

Use this when Layout Inspector confirms a problem
but you need to see exactly which frame it happens in.

---

## Production Code

Reading the compiler metrics report and fixing the root cause.

```text
// ——— Compiler metrics output ———
// OrderCard is NOT skippable.
// Reason: order: Order is unstable.
// Order has a List<OrderItem> property.
// List is unstable — Compose cannot trust it.
```

The fix is to make the type stable:

```kotlin
// Option A: annotate with @Stable (you promise it behaves stably)
@Stable
data class Order(
    val id: String,
    val items: List<OrderItem>    // your promise: equals() is reliable
)

// Option B: use ImmutableList (structurally immutable, Compose trusts it)
data class Order(
    val id: String,
    val items: ImmutableList<OrderItem>    // from kotlinx-collections-immutable
)

// In build.gradle.kts — add the library
dependencies {
    implementation("org.jetbrains.kotlinx:kotlinx-collections-immutable:0.3.7")
}

// Convert at the boundary (Repository or ViewModel)
val orders = rawOrders.map { order ->
    order.copy(items = order.items.toImmutableList())
}
```

### How To Read It

```text
ImmutableList
    ↓
kotlinx-collections-immutable provides this.
It is a List that can never be modified after creation.
Compose knows this — it treats ImmutableList as stable.
OrderCard becomes skippable.


@Stable
    ↓
A contract you make to Compose.
"I promise: if equals() returns true,
 nothing visible has changed."
Compose trusts this promise and can skip.


@Immutable
    ↓
A stronger contract.
"I promise: nothing will ever change after creation."
Use @Stable when you cannot guarantee full immutability.
Use @Immutable when you can.
```

---

## The Before / After

```text
Before fix:

Parent recomposes
    ↓
OrderList recomposes
    ↓
All OrderCards recompose (not skippable)
    ↓
847 recompositions for a simple scroll


After fix (ImmutableList):

Parent recomposes
    ↓
OrderList recomposes
    ↓
Compose compares each OrderCard's inputs
    ↓
order unchanged? → skip OrderCard
    ↓
Only new/changed cards recompose
    ↓
Smooth 60fps scrolling
```

---

> **You'll see this in...**
> - **DOC 11 — Production Compose Patterns**, where `@Stable` and `@Immutable`
>   are applied systematically across a real app's data model
> - **DOC 16 — Testing**, where compiler metrics become part of CI checks,
>   catching stability regressions before they ship

---

## Production Notes

```text
* The Compose compiler metrics file is the most valuable
  performance tool Compose provides.
  Check it whenever you suspect unnecessary recompositions.

* Add compiler metrics output to CI.
  Diff the composables.txt file across PRs.
  A composable that loses "skippable" is a regression.

* @Stable is a contract.
  Breaking it — mutating a @Stable class — causes
  missed recompositions and incorrect UI.
  The promise must be honored in all code paths.

* kotlinx-collections-immutable's ImmutableList is
  the standard fix for List stability.
  It is already used by Jetpack Compose's own internals.

* Lambda parameters are often the hidden culprit.
  onClick = { onOrderClick(order) } creates a new lambda
  on every recomposition.
  Wrap with remember(order) { { onOrderClick(order) } }
  if this becomes a bottleneck.
```

---

## Common Misconception

A common misconception is:

```text
"I should mark everything @Stable
 so all my Composables are skippable."
```

Incorrect.

```text
@Stable is a contract.

If you mark a class @Stable
and then mutate it after creation:

    ↓

Compose trusts your promise.
Skips the recomposition.
The UI shows stale data.
```

Only annotate with `@Stable` or `@Immutable`
when the contract is actually true.

Incorrect annotations cause incorrect UI —
harder to debug than unnecessary recompositions.

---

## A Natural Question

Runtime recomposition jank is now measurable and fixable.

But there is another performance problem
that happens before the user even sees the first frame.

A natural question appears:

```text
The First Time A User Opens Our App,
It Starts Slowly.
ART Is Interpreting Code, Not Running Native.
How Do We Fix First-Launch Startup?
```

That question leads us to:

```text
Baseline Profiles
```

---

## Revision

### Core Idea

```text
Compose Compiler Metrics

    ↓

A file that shows, for every Composable:
    - Is it skippable?
    - If not, which parameter is unstable?
    - What is the exact stability problem?
```

### The Three Tools

```text
Layout Inspector
    → "Which Composables are recomposing and how often?"

Compose Compiler Metrics
    → "Why can't this Composable skip?"

Composition Tracing
    → "Exactly which frame does this recomposition happen in?"
```

### The Fix Pattern

```text
Unstable type (List, MutableList, non-stable class)
    ↓
Use ImmutableList (kotlinx-collections-immutable)
or annotate with @Stable / @Immutable
    ↓
Composable becomes skippable
    ↓
Unnecessary recompositions eliminated
```

### The Contract

```text
@Stable: "equals() being true means nothing visible changed."
@Immutable: "nothing will ever change after creation."
```

### Previous Concept

```text
Why Performance Matters
```

### Next Concept

```text
Baseline Profiles
```
