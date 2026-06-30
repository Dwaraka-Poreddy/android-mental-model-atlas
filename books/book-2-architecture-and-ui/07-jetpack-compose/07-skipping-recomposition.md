# Skipping Recomposition

## Observation

Suppose we have the following screen.

```kotlin
@Composable
fun HomeScreen(count: Int) {

    Header()

    Counter(count)

    Footer()

}
```

When `count` changes,

we already know that Compose considers the recomposition scope.

Now notice something.

```text
Header()

↓

Does Not Read count

------------------------

Footer()

↓

Does Not Read count
```

If these composables don't depend on `count`,

should Compose still execute them again?

---

## A New Mental Model

Not necessarily.

Compose can recognize that re-running some composables cannot produce a different UI.

Instead of executing them again,

it simply skips them.

This optimization is called:

```text
Skipping Recomposition
```

---

## Another Example

Suppose we have:

```kotlin
@Composable
fun UserScreen(
    username: String,
    count: Int
) {

    Username(username)

    Counter(count)

}
```

The user increments the counter.

```text
count

0

↓

1
```

The `Username()` composable still receives exactly the same value.

```text
username

"Dwaraka"

↓

"Dwaraka"
```

Since nothing affecting `Username()` changed,

Compose may skip executing it,

while still recomposing:

```text
Counter()
```

---

## Mental Model

Think of recomposition as a two-step process.

```text
State Changes

↓

Determine Recomposition Scope

↓

For Each Composable

↓

Run Or Skip
```

Not every composable inside the scope is guaranteed to execute again.

Compose skips work whenever it knows the result cannot change.

---

## What Skipping Does NOT Mean

Skipping does **not** mean:

```text
Compose Ignored The State Change
```

It simply means:

```text
Compose Already Knows Running This Composable Again Cannot Change Its UI
```

So it avoids unnecessary work.

---

## Production Recognition

When someone says:

```text
"This composable was skipped."
```

think:

```text
Compose Decided There Was Nothing New To Compute
```

---

## Revision

### Core Idea

```text
Skipping Recomposition = Compose Avoiding Unnecessary Composable Executions
```

### Mental Model

```text
State Changes

↓

Recomposition Scope

↓

Compose Runs Or Skips Each Composable
```

### Production Recognition

```text
"Compose skipped this composable."
```

↓

```text
Running It Again Would Not Change The UI
```
