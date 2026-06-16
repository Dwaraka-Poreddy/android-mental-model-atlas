# Transforming Values

## A Question From The Previous Chapter

In the previous chapter we learned:

```text
Operators
```

are:

```text
Pipeline Stages
```

that can process values before they reach the collector.

A natural question appears:

```text
What If The Data I Have Is Not The Data I Need?
```

For example:

Suppose a Flow produces:

```text
Temperature In Celsius
```

but the collector needs:

```text
Temperature In Fahrenheit
```

---

Or suppose a Flow produces:

```text
User
```

but the UI needs:

```text
UserUiModel
```

How can values be changed while they travel through the pipeline?

This chapter answers that question.

---

## Revisiting The Pipeline

Recall:

```text
Producer
↓
Pipeline
↓
Collector
```

Earlier we learned:

```text
Operators
```

can perform work inside the pipeline.

A natural question appears:

```text
Can An Operator Change A Value?
```

The answer is:

```text
Yes
```

This process is called:

```text
Transformation
```

---

## First Example

Consider:

```kotlin
val numbers = flow {

    emit(1)

    emit(2)

    emit(3)
}
```

Conceptually:

```text
1
↓
Collector Receives 1
```

---

```text
2
↓
Collector Receives 2
```

---

```text
3
↓
Collector Receives 3
```

The collector receives exactly what the producer emitted.

---

A natural question appears:

```text
What If We Want

10

20

30

Instead?
```

---

## The Missing Tool

Flow provides:

```text
map()
```

for this purpose.

Conceptually:

```text
Original Value
↓
Transformation
↓
New Value
```

---

Example:

```kotlin
val numbers = flow {

    emit(1)

    emit(2)

    emit(3)
}.map { value ->

    value * 10
}
```

---

Conceptually:

```text
1
↓
10
```

---

```text
2
↓
20
```

---

```text
3
↓
30
```

The collector now receives:

```text
10

20

30
```

instead of:

```text
1

2

3
```

---

## What Does map() Do?

Think of:

```text
map()
```

as:

```text
Value In
↓
Value Out
```

---

The incoming value enters:

```text
map()
```

---

Some transformation occurs.

---

A new value leaves:

```text
map()
```

Conceptually:

```text
A
↓
map()
↓
B
```

The output value does not have to be the same as the input value.

---

## First Mental Model

Think of:

```text
map()
```

as:

```text
A Translator
```

Imagine:

```text
English
↓
Translator
↓
Spanish
```

The meaning remains related.

However:

```text
The Representation Changes
```

---

Flow transformations behave similarly.

Conceptually:

```text
Original Value
↓
map()
↓
Transformed Value
```

---

## Transforming Types

The new value does not even need to be the same type.

Consider:

```kotlin
data class User(
    val name: String
)
```

---

A Flow produces:

```kotlin
Flow<User>
```

---

However, the UI only needs:

```text
User Name
```

---

Using:

```text
map()
```

we can transform:

```kotlin
User
```

into:

```kotlin
String
```

Example:

```kotlin
users.map { user ->

    user.name
}
```

---

Conceptually:

```text
User
↓
map()
↓
String
```

---

The collector receives:

```text
Names
```

instead of:

```text
User Objects
```

---

## A Real Android Example

Imagine:

```kotlin
Flow<User>
```

coming from a repository.

---

The UI requires:

```kotlin
UserUiModel
```

instead.

---

Conceptually:

```text
Repository Data
↓
map()
↓
UI Data
```

---

Example:

```kotlin
users.map { user ->

    UserUiModel(
        displayName = user.name
    )
}
```

---

This is one of the most common uses of:

```text
map()
```

in Android applications.

---

## Another Mental Model

Imagine:

```text
Raw Ingredients
↓
Chef
↓
Prepared Meal
```

The chef receives something.

The chef transforms it.

The chef produces something new.

---

Similarly:

```text
Input Value
↓
map()
↓
Output Value
```

---

The value changes while moving through the pipeline.

---

## Relationship To The Pipeline

Earlier we learned:

```text
Producer
↓
Pipeline
↓
Collector
```

Now we can refine that model.

Conceptually:

```text
Producer
↓
map()
↓
Collector
```

---

Or:

```text
Producer
↓
Transformation
↓
Collector
```

The transformation becomes another stage in the pipeline.

---

## Another Common Misconception

A common misconception is:

```text
map() Modifies The Original Value
```

Think carefully about what actually happens.

Conceptually:

```text
Original Value
↓
map()
↓
New Value
```

---

The collector receives:

```text
The Transformed Value
```

The original value was simply used as input to create it.

---

For now, the important idea is:

```text
map() Creates The Value That Continues Through The Pipeline
```

---

## The Key Mental Model

Transformation means:

```text
Converting One Value Into Another Value
```

while it travels through the pipeline.

---

Conceptually:

```text
A
↓
map()
↓
B
```

---

```text
Input Value
↓
Transformation
↓
Output Value
```

This is the fundamental purpose of:

```text
map()
```

---

## Revision Table

| Section | Content |
|----------|----------|
| Type | Flow Transformation |
| Main Problem | Input data is not the desired output data |
| Solution | map() |
| map() Purpose | Transform values |
| Input And Output Same Type? | Not Required |
| Mental Model | Translator |
| Mental Model | Chef |
| Core Pattern | Value In → Value Out |
| Previous Concept | Flow Operators |
| Next Concept | Filtering Values |

---

## A Natural Question

We now understand:

```text
map()
```

can transform values.

A natural question appears:

```text
What If I Don't Want Certain Values At All?
```

Suppose a Flow produces:

```text
1

2

3

4

5
```

What if we only want:

```text
2

4
```

to continue through the pipeline?

How can values be:

```text
Removed
```

instead of:

```text
Transformed?
```

This question leads us to:

```text
Filtering Values
```

which we will explore next.

---

For this chapter, focus on one idea:

```text
map()
```

allows a Flow to transform:

```text
One Value
```

into:

```text
Another Value
```

while it travels through the pipeline.
