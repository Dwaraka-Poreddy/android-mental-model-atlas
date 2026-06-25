# Recomposition Scope

## Observation

Suppose we have the following screen.

```kotlin
@Composable
fun HomeScreen() {

    Header()

    Counter(count)

    Footer()

}
```

From the previous chapter, we already know that when `count` changes,

Compose runs:

```text
Counter()
```

again.

A natural question appears.

```text
What Part Of The UI Actually Recomposed?
```

---

## Recomposition Has A Scope

Compose does not think in terms of:

```text
Entire Screen
```

Instead, it thinks in terms of:

```text
Composable Functions
```

Every composable creates a potential recomposition boundary.

```text
HomeScreen()

├── Header()

├── Counter()

└── Footer()
```

When `count` changes,

Compose attempts to keep recomposition as close as possible to where the state change matters.

The portion of the UI that Compose chooses to re-run is called the:

```text
Recomposition Scope
```

---

## Mental Model

Think of recomposition as having a boundary.

```text
Entire Screen

┌──────────────────────┐
│ Header()             │
│                      │
│ Counter()   ← Runs   │
│                      │
│ Footer()             │
└──────────────────────┘
```

The recomposition scope is simply the area that Compose decides to re-execute.

---

### Another Example

Now consider a slightly different screen.

```kotlin
@Composable
fun HomeScreen() {

    CounterSection(count)

    Footer()

}

@Composable
fun CounterSection(count: Int) {

    Counter(count)

    CounterLabel(count)

}
```

Both `Counter()` and `CounterLabel()` depend on `count`.

When `count` changes,

Compose may re-run:

```text
CounterSection()

↓

Counter()

↓

CounterLabel()
```

while:

```text
Footer()
```

does not recompose.

Notice something important.

The recomposition scope is **not always a single composable**.

It can contain multiple composables,

as long as they belong to the portion of the UI that needs to be updated.

---

## Production Recognition

When someone says:

```text
"This recomposition had a small scope."
```

they mean:

```text
Only A Small Part Of The UI Was Re-run
```

Not:

```text
The Entire Screen Was Re-run
```

---

## Revision

### Core Idea

```text
Recomposition Scope = The Portion Of The UI That Compose Re-runs
```

### Mental Model

```text
State Changes

↓

Compose Determines A Scope

↓

Only That Scope Recomposes
```

### Production Recognition

```text
"Small recomposition scope"
```

↓

```text
Only A Small Part Of The UI Ran Again
```
