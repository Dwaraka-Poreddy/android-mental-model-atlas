# Recomposition

## Observation

In the previous chapter, we learned that Compose remembers the structure of our UI using a Composition Tree.

Suppose we have a simple counter screen.

Initially:

```text
Counter

0

[ + ]
```

The user taps the button.

Now the screen becomes:

```text
Counter

1

[ + ]
```

A natural question appears.

```text
CounterScreen() Already Finished Executing.

↓

So How Can Its UI Change Later?
```

---

## Think About What We Already Know

Earlier, we learned:

```text
Composable = A Function
```

Like every other function,

a composable executes,

returns,

and finishes.

```text
CounterScreen()

↓

Executed

↓

Returned
```

So how can a function that already finished produce new UI?

---

## A Familiar Example

Consider a normal Kotlin function.

```kotlin
fun greet(name: String) {
    println("Hello $name")
}
```

Call it once.

```kotlin
greet("John")
```

Output:

```text
Hello John
```

Call it again.

```kotlin
greet("Mike")
```

Output:

```text
Hello Mike
```

Nothing magical happened.

The same function simply executed again with different input.

---

## Compose Thinks The Same Way

Composable functions are still...

functions.

Compose simply calls them again whenever the UI needs to reflect the latest state.

Imagine:

```text
counter = 0

↓

CounterScreen()

↓

Counter: 0

------------------------

counter = 1

↓

CounterScreen()

↓

Counter: 1
```

The function isn't continuously running.

Compose simply executes it again.

---

## A New Mental Model

This process is called:

```text
Recomposition
```

Think of it as:

```text
State Changes

↓

Compose Runs Composable Functions Again
```

That is all recomposition means.

---

## But How Does Compose Know?

Another question naturally appears.

```text
How Does Compose Know Which Composable Function To Run Again?
```

While executing composable functions,

Compose also remembers which state each composable reads.

Imagine:

```kotlin
Header()

Counter(count)

Footer()
```

Compose remembers:

```text
Header

↓

Reads No State

------------------------

Counter

↓

Reads count

------------------------

Footer

↓

Reads No State
```

Later,

the state changes.

```kotlin
count++
```

Compose simply asks:

```text
Who Read count?
```

The answer is:

```text
Counter()
```

So Compose runs:

```text
Counter()
```

again.

It already knows which composable depends on which state.

---

## Revision

### Core Idea

```text
Recomposition = Compose Re-running Composable Functions After State Changes
```

### Mental Model

```text
State Changes

↓

Compose Knows Who ReadsThat State

↓

Compose Runs Those Composable Functions Again
```

### Production Recognition

When someone says:

```text
"This composable recomposed."
```

think:

```text
Compose Executed That Function Again Because State It Read Changed
```
