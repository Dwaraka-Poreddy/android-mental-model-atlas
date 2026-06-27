# Derived State (`derivedStateOf`)

## Observation

Throughout the Atlas,

we've repeatedly emphasized one important principle.

> **State should be the single source of truth.**

After building a few real screens,

many developers accidentally introduce another problem.

They create **too much state**.

Consider this example.

```kotlin
var firstName by remember {
    mutableStateOf("John")
}

var lastName by remember {
    mutableStateOf("Doe")
}

var fullName by remember {
    mutableStateOf("")
}
```

At first,

this looks perfectly reasonable.

We have three values.

So we create three pieces of state.

But...

is `fullName` really state?

---

## Let's Ask A Better Question

Initially,

our screen looks like this.

```text
firstName = John

lastName = Doe

fullName = John Doe
```

Everything is correct.

Now the user edits only the first name.

```text
firstName = Jane
```

What should happen next?

Someone now has to remember to write:

```kotlin
fullName = "$firstName $lastName"
```

every single time either name changes.

If we forget even once,

our UI becomes inconsistent.

```text
firstName = Jane

lastName = Doe

fullName = John Doe
```

Now we have two conflicting pieces of information on the screen.

The bug isn't in Compose.

The bug is in our state design.

---

## The Real Problem

Notice what happened.

We accidentally created two sources of truth.

```text
Source Of Truth

↓

firstName

lastName

────────────────────

Another Source

↓

fullName
```

But does `fullName` actually contain any new information?

No.

It's completely determined by:

- `firstName`
- `lastName`

It doesn't represent independent state.

It's simply derived from existing state.

---

## A Simple Rule

Before creating another piece of state,

always ask yourself:

> **Can this value be calculated entirely from existing state?**

If the answer is:

> **Yes**

then storing it as separate state is often unnecessary.

Duplicate state can become inconsistent.

Derived values cannot.

---

## The Better Solution

Instead of storing `fullName`,

simply calculate it.

```kotlin
val fullName =
    "$firstName $lastName"
```

Now there's only one source of truth.

Whenever either value changes,

`fullName` automatically reflects the latest values.

No synchronization.

No duplicate state.

No bugs.

---

## Another Example

Imagine a shopping cart.

```kotlin
val cartItems = ...
```

Should we store another state variable?

```kotlin
var totalPrice by remember {
    mutableStateOf(0.0)
}
```

Now every time:

- an item is added,
- an item is removed,
- or a quantity changes,

someone has to remember to update `totalPrice`.

Or...

we could simply calculate it.

```kotlin
val totalPrice =
    cartItems.sumOf {
        it.price * it.quantity
    }
```

Again,

there's only one source of truth.

The total can never become inconsistent with the cart.

---

## A Common Engineering Principle

This idea isn't unique to Compose.

You'll find the same principle in:

- Redux
- React
- Flutter
- Backend architecture
- Database design

Experienced engineers try to avoid storing information that can always be derived from existing data.

The fewer sources of truth,

the fewer synchronization bugs.

---

## A Natural Question

At this point,

a natural question appears.

> **If I can simply calculate derived values using normal Kotlin, why does `derivedStateOf` exist?**

Excellent question.

For simple calculations,

it usually doesn't.

For example,

this is perfectly fine.

```kotlin
val fullName =
    "$firstName $lastName"
```

You don't need `derivedStateOf`.

So when does it become useful?

---

## A Production Example

Imagine a scrolling list.

```kotlin
val listState =
    rememberLazyListState()
```

Now suppose we want to show a "Scroll to Top" button.

The rule is simple.

Show the button only after the user scrolls past the first item.

A straightforward implementation looks like this.

```kotlin
val showButton =
    listState.firstVisibleItemIndex > 0
```

Looks perfectly fine.

But let's observe what happens while the user scrolls.

```text
firstVisibleItemIndex

↓

0

1

2

3

4

5
```

Meanwhile,

our derived value becomes:

```text
showButton

↓

false

true

true

true

true

true
```

Notice something interesting.

The input changed many times.

The derived value changed only once.

Yet Compose still recalculates the expression during every recomposition.

Most of the time,

that's completely acceptable.

The calculation is tiny.

But imagine instead that:

- the computation is expensive,
- or many composables depend on the result,
- or the input changes extremely frequently.

Now those repeated calculations may become unnecessary work.

---

## Introducing `derivedStateOf`

This is the problem `derivedStateOf` solves.

```kotlin
val showButton by remember {

    derivedStateOf {

        listState.firstVisibleItemIndex > 0

    }

}
```

Compose continues observing the input.

However,

it only publishes a new derived value when the **result itself** changes.

Instead of repeatedly producing:

```text
Input

↓

1

2

3

4

5

↓

Result

↓

true

true

true

true

true
```

Compose effectively sees:

```text
Result

↓

true

(no change)

(no change)

(no change)

(no change)
```

Only meaningful changes propagate.

---

## An Analogy

Imagine a weather station.

Every second,

it reports the temperature.

```text
29.1°

29.2°

29.3°

29.2°

29.1°
```

Your UI doesn't display the exact temperature.

It only displays:

```text
Hot
```

Although the temperature changes every second,

the displayed label never changes.

```text
Hot

Hot

Hot

Hot

Hot
```

Would you notify every dependent component every second,

even though the displayed meaning never changed?

Probably not.

That's exactly the idea behind `derivedStateOf`.

The inputs may change frequently,

but the derived meaning often doesn't.

---

## Thinking Like A Production Engineer

Whenever you're about to introduce another piece of state,

ask yourself two questions.

### Question 1

Does this value represent new information?

Or can it be completely calculated from existing state?

If it can be calculated,

don't store it.

Derive it.

---

### Question 2

Is calculating this value inexpensive?

If yes,

use a normal Kotlin expression.

If the computation is expensive,

or the inputs change frequently while the result rarely changes,

consider `derivedStateOf`.

---

## A Common Misconception

Many developers think:

> **"`derivedStateOf` is how derived values should always be written in Compose."**

Not at all.

Most derived values should simply be calculated.

```kotlin
val fullName =
    "$firstName $lastName"
```

is already the correct solution.

`derivedStateOf` exists primarily as a performance optimization.

Not as a replacement for ordinary Kotlin expressions.

This is one of the most misunderstood Compose APIs.

---

## Bringing Everything Together

Whenever you need another value,

follow this decision process.

```text
Need Another Value

↓

Can It Be Calculated

From Existing State?

        │

   ┌────┴────┐

   │         │

  No        Yes

   │         │

   ▼         ▼

Store It   Compute It

                │

                ▼

     Is The Computation

     Expensive,

     Frequently Read,

     Or Based On

     Rapidly Changing Inputs?

                │

         ┌──────┴──────┐

         │             │

        No            Yes

         │             │

         ▼             ▼

Normal Kotlin   derivedStateOf
Expression
```

---

## Revision

### Core Idea

Before creating another piece of state,

ask whether the value can be completely derived from existing state.

If it can,

avoid creating another source of truth.

---

### Engineering Mindset

Don't ask:

> **"Should I store this?"**

Ask:

> **"Does this value actually represent new information?"**

If the answer is no,

derive it.

---

### Key Insight

`derivedStateOf` is not how Compose calculates derived values.

Compose can already do that with normal Kotlin expressions.

Its primary purpose is to avoid unnecessary updates when:

- inputs change frequently,
- but the derived result changes rarely.

---

## One Remaining Question

So far,

every piece of Compose state we've used has originated **inside** Compose.

A natural question appears.

> **What if the value we want to display doesn't come from Compose state at all?**

Perhaps it comes from:

- a callback,
- a listener,
- a platform API,
- or another asynchronous source outside the Compose Runtime.

How can we safely convert that external data into Compose state that automatically updates the UI?

That leads us to the next concept.

**Producing Compose State (`produceState`)**
