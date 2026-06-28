# Cold Flow

## A Question From The Previous Chapter

In the previous chapter we learned:

```text
Flow
```

is the natural choice when we need:

```text
Ongoing Updates
```

over time.

We also learned that a Flow can provide:

```text
New Values As They Become Available
```

A natural question appears:

```text
How Does A Flow Actually Provide Those Updates?
```

Suppose we have:

```kotlin
fun observeUser(): Flow<User>
```

We know this Flow can eventually provide:

```text
User Updates
```

But:

```text
When Do Those Updates Actually Start?
```

Does simply creating a Flow immediately start producing values?

Or does something else need to happen first?

Understanding the answer to this question reveals one of the most important characteristics of Flow:

```text
Cold Flow
```

This chapter explores that behavior.

---

## A Common Assumption

Many developers initially assume:

```kotlin
val userFlow = repository.observeUser()
```

means:

```text
Work Started
```

This assumption would be reasonable because many APIs behave this way.

For example:

```kotlin
scope.launch {

    loadUser()
}
```

Conceptually:

```text
launch Called
↓
Work Starts
```

---

A natural question appears:

```text
Does Flow Behave The Same Way?
```

The answer is:

```text
No
```

And understanding this difference is the purpose of this chapter.

---

## First Example

Imagine:

```kotlin
fun observeTemperature(): Flow<Int>
```

Then:

```kotlin
val temperatureFlow = observeTemperature()
```

A natural question appears:

```text
Are Temperatures Already Being Produced?
```

The answer is:

```text
No
```

At this moment:

```text
Flow Exists
```

but:

```text
No Values Are Being Produced
```

yet.

---

## The Core Idea

A Flow does not start producing values simply because:

```text
The Flow Object Exists
```

Instead:

```text
Someone Must Collect It
```

This is the most important idea in the chapter.

---

Conceptually:

```text
Flow Created
↓
No Collection
↓
No Work
```

---

Only after:

```text
Collection Starts
```

does the Flow begin producing values.

Conceptually:

```text
Flow Created
↓
Collection Starts
↓
Work Begins
```

---

## First Mental Model

Think of a Flow as:

```text
A Recipe
```

Imagine:

```text
Cake Recipe
```

written on paper.

The recipe describes:

```text
How To Make The Cake
```

---

However:

```text
Recipe Exists
```

does not mean:

```text
Cake Exists
```

Someone must actually start:

```text
Following The Recipe
```

before any work happens.

---

Conceptually:

```text
Recipe
↓
Someone Starts Cooking
↓
Cake Produced
```

A Flow behaves similarly.

---

Conceptually:

```text
Flow
↓
Someone Starts Collecting
↓
Values Produced
```

---

## A Small Code Example

Imagine:

```kotlin
val flow = flow {
    println("Starting")
    emit(1)
}
```

A natural question appears:

```text
When Does "Starting" Print?
```

Many developers initially expect:

```text
Flow Created
↓
"Starting" Prints
```

However:

```text
Nothing Happens
```

yet.

---

The code inside the Flow waits.

Only when collection begins:

```kotlin
flow.collect()
```

does the Flow start running.

Conceptually:

```text
collect()
↓
Flow Starts
↓
"Starting" Prints
↓
emit(1)
```

---

## Why Is It Called "Cold"?

The term:

```text
Cold
```

means:

```text
Inactive Until Needed
```

Think of:

```text
A Car Engine
```

that is currently:

```text
Turned Off
```

The car has the ability to move.

However:

```text
No Work Happens
```

until someone starts the engine.

---

Conceptually:

```text
Flow Exists
↓
No Collector
↓
No Work
```

---

```text
Collector Appears
↓
Work Starts
```

This behavior is why Flow is called:

```text
Cold
```

---

## Another Important Consequence

Suppose:

```kotlin
val flow = observeTemperature()
```

Nothing happens yet.

---

Now:

```kotlin
flow.collect()
```

Work begins.

---

Later:

```kotlin
flow.collect()
```

again.

A natural question appears:

```text
Will The Same Work Be Reused?
```

Usually:

```text
No
```

The Flow starts again.

Conceptually:

```text
Collect #1
↓
Work Starts
```

---

```text
Collect #2
↓
Work Starts Again
```

Each collection typically gets its own execution.

We will revisit this idea later when discussing other Flow types.

For now, simply remember:

```text
Collection Triggers Work
```

---

## Relationship To Earlier Concepts

Earlier we learned:

```text
Coroutine
```

does work when it starts running.

---

Flow introduces a different model.

Conceptually:

```text
Flow
↓
Potential Work
```

---

```text
collect()
↓
Actual Work
```

The Flow describes:

```text
What Should Happen
```

Collection decides:

```text
When It Happens
```

---

## Another Common Misconception

A common misconception is:

```text
Creating A Flow Starts The Flow
```

Incorrect.

---

Conceptually:

```text
Create Flow
↓
Nothing Happens
```

---

```text
Collect Flow
↓
Work Begins
```

---

## Another Common Misconception

A common misconception is:

```text
Flow Behaves Like launch
```

Incorrect.

Recall:

```text
launch
```

immediately starts work.

---

Flow behaves differently.

Conceptually:

```text
Flow Created
↓
Wait
↓
collect()
↓
Work Starts
```

---

## The Key Mental Model

Cold Flow means:

```text
The Flow Does Not Produce Values Until Collection Begins
```

---

Conceptually:

```text
Create Flow
↓
No Work
```

---

```text
Collect Flow
↓
Work Starts
```

Understanding this idea explains one of the most important behaviors in the entire Flow system.

---

## Revision Table

| Section | Content |
|----------|----------|
| Type | Core Flow Behavior |
| Main Idea | Flow waits for collection |
| Flow Creation Starts Work? | No |
| Collection Starts Work? | Yes |
| Mental Model | Recipe |
| Mental Model | Engine Off |
| Why Called Cold? | Inactive until needed |
| Previous Concept | Flow vs Suspend Function |
| Next Concept | Producing And Consuming Flow |

---

## A Natural Question

We now understand:

```text
A Flow Starts Working Only After Collection Begins
```

A natural question appears:

```text
What Exactly Does Collection Do?
```

How are values:

```text
Produced?
```

---

How are they:

```text
Received?
```

---

How do values travel from:

```text
Flow
```

to:

```text
Collector?
```

This question leads us to:

```text
Producing And Consuming Flow
```

where we will explore:

```text
emit()

collect()
```

and how values move through a Flow.

---

For this chapter, focus on one idea:

```text
Cold Flow
```

means:

```text
No Collector
↓
No Work
```

---

Only when:

```text
collect()
```

begins does the Flow start producing values.
