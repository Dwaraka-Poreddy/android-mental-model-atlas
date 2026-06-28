# State Read Tracking

## Observation

Imagine this screen.

```kotlin
@Composable
fun Screen() {

    Header()

    Counter()

}
```

Inside `Counter()`:

```kotlin
Text("$count")
```

Now suppose:

```kotlin
count++
```

executes.

Only:

```text
Counter()
```

recomposes.

`Header()` does not.

A simple question appears.

```text
How Did Compose Know That Header() Never Used count?
```

---

## A Thought Experiment

Imagine a classroom.

Each student borrows different library books.

The librarian keeps a record.

```text
Alice

↓

Math Book, History Book

------------------------

Bob

↓

Science Book

------------------------

Charlie

↓

Math Book
```

Later,

suppose the Math book receives a new edition.

The librarian immediately knows:

```text
Notify Alice

↓

Notify Charlie

↓

Don't Notify Bob
```

The librarian doesn't have to ask everyone which books they borrowed.

The information was already recorded.

---

## Connecting It To Compose

Compose works in a very similar way.

Whenever a composable reads Snapshot State,

the Snapshot System records that relationship.

For example,

```kotlin
Text("$count")
```

creates a relationship like this.

```text
Counter()

↓

Reads count
```

Compose now knows:

```text
Counter() Depends On count
```

That dependency is recorded automatically.

---

## A New Mental Model

Think of the Snapshot System as building a dependency map.

```text
Header()

↓

Reads Nothing

------------------------

Counter()

↓

Reads count
```

Every time a composable reads Snapshot State,

Compose records that dependency.

---

## Why State Read Tracking Exists

Later,

when:

```kotlin
count++
```

executes,

Compose doesn't need to search through every composable asking:

```text
Who Uses count?
```

It already knows.

The dependency was recorded when the UI was composed.

That makes recomposition much more efficient.

---

## The Most Important Idea

One subtle but important detail is easy to miss.

Compose doesn't discover dependencies **after** state changes.

Instead,

it discovers them **while composing the UI.**

```text
Compose Doesn't Guess Who Depends On State.

↓

It Already Knows.
```

That's one of the reasons Compose can update only the parts of the UI that actually need to change.

---

## What State Read Tracking Does NOT Do

State Read Tracking does **not**:

```text
Update The UI

↓

Perform Recomposition

↓

Store State
```

Its responsibility is much simpler.

```text
Record Which Composable Reads Which State
```

The Snapshot System uses that information later when state changes.

---

## Production Recognition

When you write:

```kotlin
Text("$count")
```

don't think:

```text
Just Displaying State
```

think:

```text
Compose Just Recorded That This Composable Depends On count
```

---

## Revision

### Core Idea

```text
State Read Tracking

↓

Record Dependencies
```

### Mental Model

```text
Composable Reads State

↓

Compose Records The Dependency
```

### Responsibilities

```text
Snapshot State

↓

Observable State

------------------------

State Read Tracking

↓

Record Who Depends On That State
```

### Production Recognition

```kotlin
Text("$count")
```

↓

```text
This Composable Depends On count
```

---

## One Remaining Question

Compose now knows:

- which state changed
- which composables depend on that state

A natural question appears.

```text
What Happens When That State Is Actually Modified?
```

That leads us to the next concept.

```text
State Write Tracking
```
