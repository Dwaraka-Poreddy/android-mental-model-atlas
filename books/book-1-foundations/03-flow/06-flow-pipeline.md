# Flow Pipeline

## A Question From The Previous Chapter

In the previous chapter we learned:

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

Can that value become:

```text
10
```

before it reaches the collector?

Can some values be ignored?

Can values be transformed before they are received?

To answer these questions we first need to understand:

```text
Flow Pipeline
```

---

## Revisiting What We Know

So far, our mental model has been:

```text
Producer
↓
Value
↓
Consumer
```

Or:

```text
emit()
↓
Value
↓
collect()
```

This model is useful.

However, it is incomplete.

A natural question appears:

```text
Does The Value Always Travel Directly To The Collector?
```

The answer is:

```text
Not Necessarily
```

---

## The Missing Piece

Between:

```text
emit()
```

and:

```text
collect()
```

there can be:

```text
Additional Steps
```

Conceptually:

```text
Producer
↓
?
↓
Collector
```

Those steps form:

```text
A Pipeline
```

through which values travel.

---

## First Mental Model

Think of:

```text
A Water Pipeline
```

Conceptually:

```text
Water Source
↓
Pipe
↓
House
```

Water does not instantly jump from:

```text
Source
```

to:

```text
House
```

It travels through:

```text
The Pipeline
```

---

Flow behaves similarly.

Conceptually:

```text
Producer
↓
Pipeline
↓
Collector
```

Values travel through the pipeline before reaching the collector.

---

## A Simple Example

Consider:

```kotlin
val numbers = flow {

    emit(1)

    emit(2)

    emit(3)
}
```

So far our mental model has been:

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

However, Flow allows additional processing to happen while values travel.

Conceptually:

```text
emit(1)
↓
Pipeline
↓
collect Receives Value
```

The pipeline becomes part of the journey.

---

## What Is A Pipeline?

A pipeline is simply:

```text
A Series Of Steps
```

through which values travel.

Conceptually:

```text
Value
↓
Step
↓
Step
↓
Step
↓
Collector
```

Each step can potentially:

```text
Inspect Data

Modify Data

Ignore Data

Combine Data
```

before the value reaches the collector.

---

## Producer And Collector

Every Flow has:

```text
A Producer
```

and:

```text
A Collector
```

Conceptually:

```text
Producer
↓
Pipeline
↓
Collector
```

---

The producer creates values.

The collector receives values.

The pipeline sits between them.

---

## Upstream And Downstream

When discussing Flow pipelines, two terms frequently appear:

```text
Upstream

Downstream
```

A simple way to think about them is:

```text
Upstream
↓
Earlier In The Pipeline
```

---

```text
Downstream
↓
Later In The Pipeline
```

---

Consider:

```text
Producer
↓
Step A
↓
Step B
↓
Collector
```

From the perspective of:

```text
Step B
```

everything before it is:

```text
Upstream
```

---

Conceptually:

```text
Producer
↓
Step A
```

are upstream.

---

Everything after it is:

```text
Downstream
```

---

Conceptually:

```text
Collector
```

is downstream.

---

## Why These Terms Exist

A common misconception is:

```text
Upstream
```

and:

```text
Downstream
```

are special Flow components.

Incorrect.

They are simply:

```text
Direction Words
```

used to describe where something sits inside a pipeline.

---

Conceptually:

```text
Earlier
↓
Upstream
```

---

```text
Later
↓
Downstream
```

---

## Another Mental Model

Imagine:

```text
A Factory Assembly Line
```

Conceptually:

```text
Raw Material
↓
Step 1
↓
Step 2
↓
Step 3
↓
Finished Product
```

The material moves through multiple stages before reaching its final destination.

---

Flow behaves similarly.

Conceptually:

```text
Producer
↓
Pipeline Stage
↓
Pipeline Stage
↓
Pipeline Stage
↓
Collector
```

Values move through multiple stages before being received.

---

## Why Pipelines Matter

Without a pipeline:

```text
Producer
↓
Collector
```

is the entire story.

---

With a pipeline:

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

Flow becomes much more powerful.

The pipeline is where most Flow operations occur.

---

## Relationship To Future Concepts

A natural question appears:

```text
What Creates These Pipeline Steps?
```

The answer is:

```text
Flow Operators
```

Operators are the tools that allow us to add processing stages into the pipeline.

We will explore those in the next chapter.

For now, simply understand:

```text
Values Travel Through A Pipeline
```

before reaching the collector.

---

## The Key Mental Model

A Flow is not simply:

```text
emit()
↓
collect()
```

Instead:

```text
emit()
↓
Pipeline
↓
collect()
```

---

Conceptually:

```text
Producer
↓
Pipeline
↓
Collector
```

Values move through the pipeline before reaching their destination.

This is the foundation for understanding Flow operators.

---

## Revision Table

| Section | Content |
|----------|----------|
| Type | Core Flow Structure |
| Main Idea | Values travel through a pipeline |
| Producer | Creates values |
| Collector | Receives values |
| Pipeline | Steps between producer and collector |
| Upstream Meaning | Earlier in pipeline |
| Downstream Meaning | Later in pipeline |
| Mental Model | Water Pipeline |
| Mental Model | Factory Assembly Line |
| Previous Concept | Producing And Consuming Flow |
| Next Concept | Flow Operators |

---

## A Natural Question

We now understand:

```text
Values Travel Through A Pipeline
```

before reaching the collector.

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

while they are moving through the Flow?

This question leads us to:

```text
Flow Operators
```

which we will explore next.

---

For this chapter, focus on one idea:

```text
Flow
```

is not simply:

```text
Producer
↓
Collector
```

---

It is:

```text
Producer
↓
Pipeline
↓
Collector
```

And values travel through that pipeline before being received.
