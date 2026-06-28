# Flow Operators

## A Question From The Previous Chapter

In the previous chapter we learned:

```text
Flow
```

is not simply:

```text
Producer
↓
Collector
```

Instead:

```text
Producer
↓
Pipeline
↓
Collector
```

Values travel through a pipeline before reaching the collector.

A natural question appears:

```text
How Do We Add New Steps To The Pipeline?
```

How can we:

```text
Modify Values?

Ignore Values?

Transform Values?

Combine Values?
```

while they are moving through a Flow?

This chapter answers that question.

---

## Revisiting The Pipeline

Consider:

```kotlin
val numbers = flow {

    emit(1)

    emit(2)

    emit(3)
}
```

And:

```kotlin
numbers.collect { value ->

    println(value)
}
```

Conceptually:

```text
1
↓
Collector
```

---

```text
2
↓
Collector
```

---

```text
3
↓
Collector
```

A natural question appears:

```text
What If We Want Different Values To Reach The Collector?
```

For example:

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

## The Missing Tool

To modify values while they travel through a pipeline, Flow provides:

```text
Operators
```

Think of operators as:

```text
Pipeline Stages
```

that perform work on values before they reach the collector.

---

Conceptually:

```text
Producer
↓
Operator
↓
Collector
```

---

Or:

```text
Producer
↓
Operator
↓
Operator
↓
Operator
↓
Collector
```

---

Each operator becomes a new stage in the pipeline.

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

Now imagine an operator that multiplies values by:

```text
10
```

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

The collector would receive:

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

The important idea is:

```text
Values Can Change While Moving Through The Pipeline
```

---

## Another Example

Imagine:

```text
1

2

3

4

5
```

traveling through the pipeline.

Suppose an operator removes:

```text
Odd Numbers
```

Conceptually:

```text
1
↓
Removed
```

---

```text
2
↓
Allowed
```

---

```text
3
↓
Removed
```

---

```text
4
↓
Allowed
```

---

```text
5
↓
Removed
```

The collector would only receive:

```text
2

4
```

---

The values changed because:

```text
The Pipeline Performed Work
```

before they reached the collector.

---

## First Mental Model

Think of a pipeline operator as:

```text
A Factory Worker
```

Imagine:

```text
Raw Material
↓
Worker
↓
Finished Material
```

The worker receives something.

The worker performs work.

The worker passes along the result.

---

Flow operators behave similarly.

Conceptually:

```text
Value
↓
Operator
↓
Updated Value
```

---

Each operator becomes another worker in the assembly line.

---

## Multiple Operators

A pipeline can contain more than one operator.

Conceptually:

```text
Producer
↓
Operator A
↓
Operator B
↓
Operator C
↓
Collector
```

---

A value may pass through:

```text
Several Stages
```

before finally reaching the collector.

---

For example:

```text
1
↓
Multiply By 10
↓
Keep Even Numbers
↓
Collector
```

The value changes as it moves through the pipeline.

---

## Why Operators Exist

Without operators:

```text
Producer
↓
Collector
```

is the only possible flow.

---

With operators:

```text
Producer
↓
Processing
↓
Processing
↓
Processing
↓
Collector
```

we can shape the data before it reaches its destination.

This is one of the most powerful features of Flow.

---

## Intermediate Operators

Most operators you will encounter are:

```text
Intermediate Operators
```

An intermediate operator is simply:

```text
A Pipeline Stage
```

that receives values and passes values onward.

Conceptually:

```text
Value In
↓
Work Happens
↓
Value Out
```

---

The important idea is not the terminology.

The important idea is:

```text
Values Continue Moving Through The Pipeline
```

after the operator finishes its work.

---

We will see many examples of these operators in upcoming chapters.

---

## Relationship To Future Concepts

A natural question appears:

```text
What Specific Operators Can We Use?
```

How do we:

```text
Transform Values?

Filter Values?

Combine Values?
```

using actual Flow APIs?

The next few chapters will answer those questions.

For now, focus on understanding:

```text
Why Operators Exist
```

before learning individual operators.

---

## The Key Mental Model

Flow operators are:

```text
Pipeline Stages
```

that perform work on values before they reach the collector.

---

Conceptually:

```text
Producer
↓
Operator
↓
Collector
```

---

Or:

```text
Producer
↓
Operator
↓
Operator
↓
Operator
↓
Collector
```

Understanding operators as:

```text
Pipeline Stages
```

makes the rest of Flow significantly easier to understand.

---

## Revision Table

| Section | Content |
|----------|----------|
| Type | Core Flow Concept |
| Main Purpose | Modify values in the pipeline |
| Operators Act As | Pipeline stages |
| Operator Mental Model | Factory worker |
| Intermediate Operator | Receives values and passes values onward |
| Core Pattern | Producer → Operator → Collector |
| Previous Concept | Flow Pipeline |
| Next Concept | Common Operators |

---

## A Natural Question

We now understand:

```text
Why Operators Exist
```

A natural question appears:

```text
What Operators Will I Use Most Often?
```

How can we:

```text
Transform Values?

Filter Values?

Combine Values?
```

using real Flow APIs?

This question leads us to:

```text
Common Operators
```

where we will explore:

```text
map

filter

debounce

combine

zip

flatMapLatest
```

and understand the problems each one solves.

---

For this chapter, focus on one idea:

```text
Operators
```

are:

```text
Pipeline Stages
```

that allow values to be processed before reaching the collector.
