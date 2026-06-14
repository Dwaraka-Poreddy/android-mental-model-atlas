# CoroutineExceptionHandler

## A Question From The Previous Chapter

In the previous chapter we learned:

```text
Unhandled Exceptions Do Not Disappear
```

Instead:

```text
Failure Information Moves Through The Coroutine Hierarchy
```

A natural question appears:

```text
Who Eventually Notices The Failure?
```

If an exception is not handled using:

```text
try-catch
```

some mechanism must still be able to observe it.

This leads us to:

```text
CoroutineExceptionHandler
```

---

## The Problem

Consider:

```kotlin
launch {

    throw RuntimeException("Boom")
}
```

Suppose there is:

```text
No try-catch
```

A natural question appears:

```text
What Happens To The Exception?
```

Earlier we learned:

```text
Exception Propagation
```

moves failure information through the hierarchy.

However:

```text
Somebody Must Eventually Observe The Failure
```

Otherwise:

```text
Failures Would Simply Vanish
```

making debugging extremely difficult.

---

## What Is CoroutineExceptionHandler?

Conceptually:

```text
CoroutineExceptionHandler
```

provides a place to:

```text
Observe Unhandled Failures
```

---

Think of it as:

```text
A Failure Observer
```

rather than:

```text
A Failure Fixer
```

This distinction is extremely important.

---

## First Mental Model

Suppose:

```text
Exception Thrown
↓
Not Handled
↓
Propagation Happens
↓
Someone Observes Failure
```

That final observer can be:

```text
CoroutineExceptionHandler
```

---

Conceptually:

```text
Unhandled Failure
↓
CoroutineExceptionHandler Notified
```

---

## A Simple Example

```kotlin
val handler = CoroutineExceptionHandler { _, exception ->

    println("Observed: $exception")
}
```

---

Then:

```kotlin
scope.launch(handler) {

    throw RuntimeException("Boom")
}
```

Conceptually:

```text
Exception Thrown
↓
Not Handled
↓
Propagation Happens
↓
Handler Observes Failure
```

---

The handler receives information about the failure.

---

## What Does The Handler Do?

Common uses include:

```text
Logging

Crash Reporting

Analytics

Monitoring

Debugging
```

The handler is often used to:

```text
Record What Happened
```

rather than:

```text
Prevent It
```

---

## A Very Important Distinction

A common misconception is:

```text
CoroutineExceptionHandler = try-catch
```

Incorrect.

They solve different problems.

---

Think of:

```text
try-catch
```

as:

```text
Handle Failure
```

---

Think of:

```text
CoroutineExceptionHandler
```

as:

```text
Observe Unhandled Failure
```

---

Conceptually:

```text
try-catch
↓
Responds To Failure
```

---

```text
CoroutineExceptionHandler
↓
Learns About Failure
```

This is the most important idea in this chapter.

---

## Another Mental Model

Imagine a fire.

---

Think of:

```text
try-catch
```

as:

```text
Firefighter
```

The firefighter actively responds to the problem.

---

Think of:

```text
CoroutineExceptionHandler
```

as:

```text
Incident Reporter
```

The incident reporter records:

```text
What Happened
```

after the event occurs.

---

Both are useful.

However:

```text
They Serve Different Roles
```

---

## Does The Handler Prevent Failure?

A natural question appears:

```text
If A Handler Exists

Does The Coroutine Avoid Failure?
```

No.

The failure has already occurred.

---

Conceptually:

```text
Exception Thrown
↓
Coroutine Failed
↓
Handler Observes Failure
```

Notice:

```text
Handler Runs After Failure
```

not:

```text
Instead Of Failure
```

---

This distinction is very important.

---

## Real Android Example

Imagine:

```kotlin
val handler = CoroutineExceptionHandler { _, throwable ->

    logCrash(throwable)
}
```

---

Then:

```kotlin
viewModelScope.launch(handler) {

    repository.syncData()
}
```

Suppose:

```text
syncData()
```

fails unexpectedly.

Conceptually:

```text
Sync Fails
↓
Unhandled Exception
↓
Handler Logs Failure
```

This is a common real-world use case.

---

## Another Example

```kotlin
val handler = CoroutineExceptionHandler { _, throwable ->

    analytics.recordFailure(throwable)
}
```

---

Conceptually:

```text
Failure Happens
↓
Handler Records Failure
```

Applications often use handlers to improve:

```text
Monitoring

Diagnostics

Crash Investigation
```

---

## Another Common Misconception

A common misconception is:

```text
CoroutineExceptionHandler Recovers From Failure
```

Incorrect.

The handler does not magically make the failure disappear.

---

Conceptually:

```text
Failure Happens
↓
Handler Observes Failure
```

The failure still occurred.

---

## Another Common Misconception

A common misconception is:

```text
CoroutineExceptionHandler Prevents Exceptions
```

Incorrect.

Exceptions still occur.

The handler simply receives information about them.

---

## Relationship To Earlier Concepts

Recall:

```text
Exception Handling
```

introduced:

```text
try-catch
```

for handling failures.

---

Recall:

```text
Exception Propagation
```

introduced:

```text
Unhandled Failure Moving Through The Hierarchy
```

---

This chapter answers:

```text
Who Observes That Failure?
```

Conceptually:

```text
Unhandled Failure
↓
Propagation
↓
CoroutineExceptionHandler Observes Failure
```

---

## The Key Mental Model

```text
try-catch
```

and:

```text
CoroutineExceptionHandler
```

are not competitors.

They solve different problems.

---

```text
try-catch
↓
Handles Failure
```

---

```text
CoroutineExceptionHandler
↓
Observes Unhandled Failure
```

Understanding this distinction explains why Kotlin provides both mechanisms.

---

## Revision Table

| Section | Content |
|----------|----------|
| Type | Failure Observation Mechanism |
| Main Purpose | Observe unhandled failures |
| Replaces try-catch? | No |
| Handles Failure? | No |
| Observes Failure? | Yes |
| Common Usage | Logging |
| Common Usage | Monitoring |
| Common Usage | Crash Reporting |
| Previous Concept | Exception Propagation |
| Next Concept | launch vs async Exceptions |

---

## A Natural Question

So far we have discussed:

```text
Coroutine Failures
```

without paying much attention to:

```text
Which Coroutine Builder Created The Coroutine
```

However, earlier we learned:

```text
launch

async
```

create different kinds of coroutines.

---

Recall:

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
Do Failures Behave The Same Way For Both Builders?
```

Or does:

```text
launch
```

behave differently from:

```text
async
```

when exceptions occur?

This question leads us to:

```text
launch vs async Exceptions
```

which we will explore next.

---

For this chapter, focus on one idea:

```text
CoroutineExceptionHandler
```

does not:

```text
Handle Failure
```

It:

```text
Observes Unhandled Failure
```
