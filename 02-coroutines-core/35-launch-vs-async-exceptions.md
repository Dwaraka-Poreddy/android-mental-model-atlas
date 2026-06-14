# launch vs async Exceptions

## A Question From The Previous Chapter

In the previous chapter we learned:

```text
CoroutineExceptionHandler
```

can observe:

```text
Unhandled Failures
```

A natural question appears:

```text
Do Failures Behave The Same Way For All Coroutine Builders?
```

Earlier we learned:

```text
launch
↓
Returns Job
```

---

```text
async
↓
Returns Deferred
```

A natural question appears:

```text
Do Exceptions Behave Differently For launch And async?
```

This chapter answers that question.

---

## Why Might They Behave Differently?

Recall:

```text
launch
↓
Returns Job
```

A Job tracks lifecycle information.

It does not represent:

```text
A Future Result
```

---

Recall:

```text
async
↓
Returns Deferred
```

A Deferred represents:

```text
A Future Result
```

This difference turns out to be extremely important.

A natural question appears:

```text
If Deferred Can Hold A Future Result

Can It Also Hold A Failure?
```

The answer is:

```text
Yes
```

And that explains the difference between:

```text
launch
```

and:

```text
async
```

when exceptions occur.

---

## launch Failure Flow

Consider:

```kotlin
launch {

    throw RuntimeException("Boom")
}
```

Conceptually:

```text
Exception Happens
↓
No Result Container Exists
↓
Failure Reported
```

Because:

```text
Job
```

does not represent a future result.

There is nowhere to store the failure for later retrieval.

---

## async Failure Flow

Consider:

```kotlin
val deferred = async {

    throw RuntimeException("Boom")
}
```

Conceptually:

```text
Exception Happens
↓
Deferred Exists
↓
Failure Stored
```

Unlike:

```text
Job
```

a:

```text
Deferred
```

already exists to hold future outcomes.

Those outcomes can be:

```text
Success
```

or:

```text
Failure
```

---

## Enter await()

Earlier we learned:

```text
await()
```

retrieves the result from:

```text
Deferred
```

A natural question appears:

```text
What Happens If Deferred Contains A Failure?
```

Conceptually:

```text
await()
↓
Request Result
↓
Failure Revealed
```

---

Example:

```kotlin
val deferred = async {

    throw RuntimeException("Boom")
}

deferred.await()
```

Conceptually:

```text
Failure Stored
↓
await()
↓
Failure Revealed
```

This is the key behavior that often surprises developers.

---

## Another Common Misconception

A common misconception is:

```text
async Swallows Exceptions
```

Incorrect.

The exception still exists.

Conceptually:

```text
Failure Happens
↓
Failure Stored
↓
await() Reveals Failure
```

Nothing disappeared.

The failure was simply waiting inside:

```text
Deferred
```

until somebody requested the result.

---

## Relationship To Earlier Concepts

Earlier we learned:

```text
Deferred
```

represents:

```text
A Future Result
```

This chapter extends that idea.

Conceptually:

```text
Deferred
```

can hold:

```text
Success Result
```

or:

```text
Failure Information
```

until:

```text
await()
```

requests it.

---

## The Key Mental Model

The difference between:

```text
launch
```

and:

```text
async
```

comes directly from:

```text
Job
vs
Deferred
```

---

Conceptually:

```text
launch
↓
Returns Job
↓
Failure Reported
```

---

```text
async
↓
Returns Deferred
↓
Failure Stored
↓
await() Reveals It
```

Understanding this explains why exceptions appear to behave differently between the two builders.

---

## Revision Table

| Section | Content |
|----------|----------|
| Type | Builder Failure Behavior |
| Builder 1 | launch |
| Returns | Job |
| launch Failure Behavior | Failure Reported |
| Builder 2 | async |
| Returns | Deferred |
| async Failure Behavior | Failure Stored |
| Revealed By | await() |
| Previous Concept | CoroutineExceptionHandler |
| Next Concept | Flow |

---

## Looking Back At Coroutines Core

At this point we have explored:

```text
Coroutine Creation

Suspension

Continuation

Scopes

Jobs

Structured Concurrency

Dispatchers

Cancellation

Exception Handling
```

We now understand how individual coroutines:

```text
Start

Run

Suspend

Resume

Cancel

Fail
```

A natural question appears:

```text
How Does Data Move Between Coroutines?
```

Real applications constantly produce and consume data:

```text
Network Responses

Database Updates

User Input

UI State Changes
```

How can coroutines represent streams of changing values?

This question leads us to:

```text
Flow
```

which begins the next major section of the Atlas.

---

For this chapter, focus on one idea:

```text
launch
```

and:

```text
async
```

both use exceptions.

The difference is:

```text
launch
↓
Failure Reported
```

while:

```text
async
↓
Failure Stored
↓
await() Reveals It
```
