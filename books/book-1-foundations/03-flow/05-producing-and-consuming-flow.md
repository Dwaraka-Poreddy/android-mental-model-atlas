# Producing And Consuming Flow

## A Question From The Previous Chapter

In the previous chapter we learned:

```text
Cold Flow
```

means:

```text
No Collector
↓
No Work
```

and:

```text
collect()
↓
Work Starts
```

A natural question appears:

```text
What Exactly Does collect() Do?
```

And:

```text
Where Do Flow Values Actually Come From?
```

This chapter answers those questions.

---

## Revisiting Flow

Earlier we learned:

```text
Flow
```

represents:

```text
A Stream Of Values
```

Conceptually:

```text
Value
↓
Value
↓
Value
↓
Value
```

A natural question appears:

```text
Who Produces Those Values?
```

And:

```text
Who Receives Those Values?
```

To answer these questions we need to understand two important APIs:

```text
emit()

collect()
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

A natural question appears:

```text
Where Did
1
2
3
Come From?
```

The answer is:

```text
emit()
```

---

Conceptually:

```text
emit(1)
↓
Produces 1
```

---

```text
emit(2)
↓
Produces 2
```

---

```text
emit(3)
↓
Produces 3
```

---

## The Purpose Of emit()

Think of:

```text
emit()
```

as:

```text
Produce A Value
```

or:

```text
Send A Value
```

into the Flow.

---

Conceptually:

```text
emit(value)
↓
Value Produced
```

---

Every time:

```text
emit()
```

is called,

a new value enters the Flow.

---

## Receiving Values

Now consider:

```kotlin
numbers.collect { value ->
    println(value)
}
```

A natural question appears:

```text
What Does collect() Do?
```

The answer is:

```text
Receive Values
```

from the Flow.

---

Conceptually:

```text
collect()
↓
Wait For Value
↓
Receive Value
```

---

Every time a new value arrives:

```text
collect()
```

receives it.

---

## Following The Complete Journey

Consider:

```kotlin
val numbers = flow {
    emit(1)
    emit(2)
    emit(3)
}

numbers.collect { value ->
    println(value)
}
```

Conceptually:

```text
emit(1)
↓
collect Receives 1
```

---

```text
emit(2)
↓
collect Receives 2
```

---

```text
emit(3)
↓
collect Receives 3
```

This is the complete journey of a value through the Flow.

---

## First Mental Model

Think of:

```text
emit()
```

as:

```text
A Producer
```

---

Think of:

```text
collect()
```

as:

```text
A Consumer
```

---

Conceptually:

```text
Producer
↓
Value
↓
Consumer
```

---

Every Flow fundamentally follows this pattern.

---

## Another Mental Model

Imagine:

```text
A Speaker
```

and:

```text
A Listener
```

---

Conceptually:

```text
Speaker
↓
Message
↓
Listener
```

---

The speaker sends messages.

The listener receives messages.

---

Similarly:

```text
emit()
↓
Sends Values
```

---

```text
collect()
↓
Receives Values
```

---

## Multiple Values

One important detail is:

```text
collect()
```

does not receive:

```text
Only One Value
```

It continues receiving values as they arrive.

---

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
1 Arrives
↓
collect Receives It
```

---

```text
2 Arrives
↓
collect Receives It
```

---

```text
3 Arrives
↓
collect Receives It
```

---

This is why:

```text
Flow
```

works naturally with:

```text
Streams Of Data
```

rather than:

```text
Single Results
```

---

## Another Common Misconception

A common misconception is:

```text
collect() Creates Values
```

Incorrect.

---

Values are produced by:

```text
emit()
```

---

Values are received by:

```text
collect()
```

---

Conceptually:

```text
emit()
↓
Produces
```

---

```text
collect()
↓
Receives
```

---

## Another Common Misconception

A common misconception is:

```text
Flow Stores All Values
```

Incorrect.

For now, think of Flow as:

```text
Moving Values
```

from:

```text
Producer
```

to:

```text
Consumer
```

---

Conceptually:

```text
emit(value)
↓
Value Travels
↓
collect Receives Value
```

Later chapters will explore more advanced behaviors.

---

## Relationship To Cold Flow

Earlier we learned:

```text
Cold Flow
```

means:

```text
No Collector
↓
No Work
```

Now we can refine that understanding.

---

Conceptually:

```text
No Collector
↓
No collect()
↓
No Values Produced
```

---

Only after:

```text
collect()
```

begins does the Flow start producing values.

---

Conceptually:

```text
collect()
↓
Flow Starts
↓
emit() Produces Values
```

This connects Cold Flow with:

```text
emit()
```

and:

```text
collect()
```

---

## The Key Mental Model

Every Flow fundamentally contains:

```text
A Producer
```

and:

```text
A Consumer
```

---

Conceptually:

```text
emit()
↓
Produces Value
```

---

```text
collect()
↓
Receives Value
```

---

```text
Producer
↓
Value
↓
Consumer
```

Understanding this movement of values is the foundation for everything that follows in Flow.

---

## Revision Table

| Section | Content |
|----------|----------|
| Type | Core Flow Mechanics |
| Produces Values | emit() |
| Receives Values | collect() |
| emit Mental Model | Producer |
| collect Mental Model | Consumer |
| Alternative Mental Model | Speaker → Listener |
| Core Flow Pattern | Producer → Value → Consumer |
| Previous Concept | Cold Flow |
| Next Concept | Flow Pipeline |

---

## A Natural Question

We now understand:

```text
emit()
```

produces values and:

```text
collect()
```

receives values.

A natural question appears:

```text
Can Values Be Changed Before They Reach collect()?
```

For example:

Suppose:

```text
emit(1)
```

produces a value.

Can we transform it into:

```text
10
```

before it reaches the collector?

---

Can we ignore certain values?

Can we combine multiple streams of values?

Can we perform operations on data as it moves through a Flow?

Understanding these possibilities requires us to understand:

```text
Flow Pipeline
```

which we will explore next.

---

For this chapter, focus on one idea:

```text
emit()
↓
Produces Values
```

---

```text
collect()
↓
Receives Values
```

---

Together they create the fundamental Flow pattern:

```text
Producer
↓
Value
↓
Consumer
```
