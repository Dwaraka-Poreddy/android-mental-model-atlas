# remember

## Observation

In the previous chapters, we learned:

```text
Composable = A Function
```

and

```text
Recomposition

↓

Composable Functions Run Again
```

Now consider this composable.

```kotlin
@Composable
fun ProfileScreen() {

    val randomNumber = generateRandomNumber()

    Text("$randomNumber")

}
```

A simple question appears.

```text
What Happens When ProfileScreen() Recomposes?
```

Think about what we already know.

```text
ProfileScreen() Runs Again
```

which means:

```kotlin
generateRandomNumber()
```

runs again.

So another question naturally follows.

```text
Wouldn't A New Random Number Be Generated Every Recomposition?
```

If composable functions run again,

how can any value survive between recompositions?

---

## The Problem

Local variables belong to a function call.

Every time a function executes,

its local variables are created again.

```text
First Execution

↓

Generate 5831

------------------------

Second Execution

↓

Generate 9247

------------------------

Third Execution

↓

Generate 3184
```

This is how normal functions work.

Composable functions are no different.

So local variables alone cannot preserve values across recompositions.

---

## A New Mental Model

Compose solves this problem using:

```kotlin
remember { ... }
```

Think of it as:

```text
Don't Create A New Value Every Time.

↓

Reuse The Existing Value Across Recompositions.
```

Instead of recreating the value on every function execution,

Compose gives the composable the same value that it remembered from the previous recomposition.

---

## Example

```kotlin
@Composable
fun ProfileScreen() {

    val randomNumber = remember {
        generateRandomNumber()
    }

    Text("$randomNumber")

}
```

Now,

`generateRandomNumber()` runs only the first time this composable enters the composition.

During future recompositions,

Compose simply returns the remembered value.

---

## What remember Does

Without `remember`:

```text
Recomposition

↓

Generate A New Value
```

With `remember`:

```text
Recomposition

↓

Reuse The Previous Value
```

That is the entire purpose of `remember`.

---

## What remember Does NOT Do

`remember` does **not**:

```text
Survive Configuration Changes Or Process Death
```

It only remembers values while the current composition is alive.

We'll learn how to handle those situations later.

---

## Production Recognition

When you see:

```kotlin
val user = remember {
    loadCurrentUser()
}
```

don't think:

```text
Run loadCurrentUser() Every Recomposition
```

think:

```text
Run It Once

↓

Reuse The Result During Future Recompositions
```

---

## Revision

### Core Idea

```text
remember = Reuse The Same Value Across Recompositions
```

### Mental Model

```text
Without remember

↓

New Value Every Recomposition

------------------------

With remember

↓

Reuse Previous Value
```

### Production Recognition

```kotlin
remember { ... }
```

↓

```text
Keep This Value Alive Across Recompositions
```
