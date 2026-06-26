# Skipping

## Observation

Earlier, we learned that Restart Groups tell Compose where recomposition should begin.

Imagine this screen.

```kotlin
@Composable
fun Screen() {

    Header(title = "Home")

    Counter(count)

}
```

Now suppose:

```kotlin
count++
```

executes.

Compose reaches:

```kotlin
Header(title = "Home")
```

A simple question appears.

```text
Compose reached Header().

↓

How does it know whether it needs to run it again?
```

---

## A Thought Experiment

Imagine you're proofreading a document.

Someone tells you that only page 12 was edited.

When you reach page 1,

you compare it with yesterday's version.

```text
Same

↓

Skip
```

Later,

you reach page 12.

```text
Different

↓

Review
```

You don't reread every page.

You compare first,

then decide.

---

## Connecting It To Compose

Compose works in a very similar way.

When recomposition reaches a composable,

it first checks whether anything that affects that composable has changed.

Think of it like this.

```text
Reached Composable

↓

Anything Changed?

↓

No → Skip

Yes → Recompose
```

Skipping is a decision,

not a default behavior.

---

## A New Mental Model

Every Restart Group becomes a decision point.

```text
Restart Group

↓

Compare Inputs

↓

Changed?

↓

Yes → Recompose

No → Skip
```

Compose avoids unnecessary work by comparing what matters before running the composable again.

---

## Why Skipping Exists

Imagine Compose never compared anything.

Every recomposition would execute every composable inside the Restart Group,

even if nothing had changed.

Instead,

Compose first asks:

```text
Did anything relevant change?
```

If the answer is **No**,

there's no reason to execute that composable again.

---

## What Skipping Does NOT Mean

Skipping does **not** mean Compose ignored the composable.

It means Compose determined that rerunning it would produce the same result,

so it safely reused the previous work.

---

## Production Recognition

When recomposition reaches:

```kotlin
Header(title = "Home")
```

don't think:

```text
Compose always runs Header().
```

think:

```text
Compose first checks whether Header() needs to run. If nothing relevant changed, it skips it.
```

---

## Revision

### Core Idea

```text
Reached Composable

↓

Compare Inputs

↓

Skip or Recompose
```

### Mental Model

```text
Restart Group

↓

Decision Point

↓

Skip or Recompose
```

### Why It Exists

```text
Avoid unnecessary work.
```

### Production Recognition

```kotlin
Header(title = "Home")
```

↓

```text
Compare first. Run only if needed.
```

---

## One Remaining Question

Compose compares a composable's inputs before deciding whether to skip it.

A natural question appears.

```text
How does Compose know those comparisons are reliable?
```

That leads us to the next concept.

```text
Stability
```
