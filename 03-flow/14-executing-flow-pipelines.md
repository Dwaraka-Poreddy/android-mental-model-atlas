# Executing Flow Pipelines

## A Question From The Previous Chapter

Over the last few chapters we have built increasingly sophisticated Flow pipelines.

For example:

```kotlin
val searchResultsFlow =
    searchQueryFlow
        .debounce(300)
        .flatMapLatest { query ->
            repository.search(query)
        }
        .map { results ->
            results.sortedBy { it.name }
        }
```

A natural question appears:

```text
When Does Any Of This Actually Start Running?
```

We have:

```text
debounce()

flatMapLatest()

map()
```

all connected together.

Are they already executing?

Or is something still missing?

This chapter answers that question.

---

## Building A Pipeline

Consider:

```kotlin
val numbers =
    flowOf(1, 2, 3)
        .map { it * 2 }
        .filter { it > 2 }
```

Conceptually we have built:

```text
Producer
↓

map()

↓

filter()
```

A natural question appears:

```text
Are Values Already Flowing?
```

The answer is:

```text
No.
```

At this point we have only:

```text
Built A Pipeline
```

Nothing is executing yet.

---

## Another Mental Model

Imagine building a water pipeline.

Conceptually:

```text
Reservoir

↓

Pipe

↓

Filter

↓

Valve

↓

Tap
```

Everything has been connected together.

However:

```text
No Water Is Flowing
```

The pipeline exists.

But it is still:

```text
Inactive
```

---

## A Blueprint

Another way to think about it is:

```text
Pipeline = Blueprint
```

It describes:

```text
How Data Should Move
```

It does not mean:

```text
Data Is Already Moving
```

---

## Executing The Pipeline

Eventually we write:

```kotlin
numbers.collect { value ->

    println(value)
}
```

Something new happens.

Conceptually:

```text
Producer

↓

map()

↓

filter()

↓

collect()
```

Now:

```text
Execution Begins
```

Values start flowing through every operator.

---

## What Does collect() Do?

Many beginners think:

```text
collect() Gets Values
```

While that is true, a more useful mental model is:

```text
collect() Starts The Entire Pipeline
```

Conceptually:

```text
collect()

↓

filter()

↓

map()

↓

Producer
```

Every part of the pipeline begins participating.

---

## Another Mental Model

Imagine a recipe.

You write:

```text
Ingredients

↓

Preparation

↓

Cooking Steps
```

You now have:

```text
A Recipe
```

Nothing has been cooked yet.

Only when someone starts following the recipe:

```text
Preparation Begins
```

Similarly:

```text
map()

filter()

debounce()

combine()

flatMapLatest()
```

describe:

```text
How Data Should Be Processed
```

They do not process anything immediately.

---

## Building vs Executing

Every Flow operator belongs to one of two categories.

---

### Building Operators

These operators continue building the pipeline.

Examples:

```text
map()

filter()

debounce()

combine()

flatMapLatest()
```

Conceptually:

```text
Input Flow
↓

Describe More Work
↓

Output Flow
```

They produce:

```text
Another Flow
```

allowing additional operators to be chained.

---

### Executing Operators

Eventually something must say:

```text
Start
```

These operators begin observing the pipeline.

Examples:

```text
collect()

first()

single()

toList()
```

Conceptually:

```text
Pipeline

↓

Execution
```

---

## Intermediate Operators

The formal name for:

```text
map()

filter()

debounce()

combine()

flatMapLatest()
```

is:

```text
Intermediate Operators
```

They are called intermediate because they exist:

```text
Between The Producer And The Final Consumer
```

Conceptually:

```text
Producer

↓

Intermediate Operators

↓

Terminal Operator
```

---

## Terminal Operators

The formal name for:

```text
collect()

first()

single()

toList()
```

is:

```text
Terminal Operators
```

They are called terminal because they:

```text
Finish The Pipeline
```

and begin execution.

---

## first()

Consider:

```kotlin
val firstNumber =
    numbers.first()
```

A natural question appears:

```text
Why Is first() A Terminal Operator?
```

Because it:

```text
Starts Collection
```

but stops after receiving:

```text
The First Value
```

Conceptually:

```text
Producer

↓

Pipeline

↓

First Value

↓

Stop
```

---

## single()

Consider:

```kotlin
val onlyUser =
    userFlow.single()
```

Conceptually:

```text
Start Collection

↓

Expect Exactly One Value

↓

Return It
```

---

If more than one value exists:

```text
single()

Fails
```

because its contract has been violated.

---

## toList()

Consider:

```kotlin
val numbersList =
    numbers.toList()
```

Conceptually:

```text
Start Collection

↓

Receive Every Value

↓

Build List

↓

Return List
```

---

Unlike:

```text
first()
```

which stops early,

```text
toList()
```

continues until the Flow finishes.

---

## Code Walkthrough

Consider:

```kotlin
flowOf(1, 2, 3)
    .map { it * 2 }
    .filter { it > 2 }
    .collect { println(it) }
```

Conceptually:

```text
1
↓

2
↓

Removed

----------------

2
↓

4
↓

Collected

----------------

3
↓

6
↓

Collected
```

Output:

```text
4

6
```

---

## Another Common Misconception

A common misconception is:

```text
map() Immediately Transforms Values
```

Incorrect.

It only describes:

```text
Future Transformation
```

The transformation occurs only after:

```text
A Terminal Operator Starts Collection
```

---

## Another Common Misconception

A common misconception is:

```text
Intermediate Operators Execute One By One
```

Incorrect.

They form:

```text
One Connected Pipeline
```

Execution begins only when a:

```text
Terminal Operator
```

appears.

---

## The Key Mental Model

Building a Flow pipeline and executing a Flow pipeline are two different steps.

Conceptually:

```text
Producer

↓

map()

↓

filter()

↓

debounce()

↓

combine()

↓

flatMapLatest()
```

represents:

```text
Pipeline Construction
```

Nothing is executing yet.

---

Adding:

```text
collect()

first()

single()

toList()
```

changes the picture:

```text
Producer

↓

Intermediate Operators

↓

Terminal Operator
```

Now:

```text
Values Begin Moving
```

---

## Revision Table

| Section | Content |
|----------|----------|
| Main Idea | Building and executing are different steps |
| Building Operators | Create or extend a Flow pipeline |
| Executing Operators | Start observing the pipeline |
| Intermediate Operators | Build pipelines |
| Terminal Operators | Execute pipelines |
| `collect()` | Observe every value |
| `first()` | Observe first value |
| `single()` | Expect exactly one value |
| `toList()` | Collect all values |
| Previous Concept | Keeping Only The Latest Result |
| Next Concept | Flow In Real Applications |

---

## A Natural Question

We now understand:

```text
How To Build Flow Pipelines
```

and:

```text
How To Execute Flow Pipelines
```

A natural question appears:

```text
Where Do Android Applications Actually Use These Patterns?
```

Why would an application use:

```text
combine()

flatMapLatest()

debounce()

map()
```

together?

How do repositories, ViewModels and UI layers communicate using Flow?

This question leads us to:

```text
Flow In Real Applications
```

which we will explore next.

---

For this chapter, focus on one idea:

```text
Intermediate Operators Build Pipelines.
```

---

```text
Terminal Operators Execute Pipelines.
```

Understanding this distinction makes every Flow pipeline much easier to reason about.
