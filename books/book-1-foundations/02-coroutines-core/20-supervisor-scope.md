# supervisorScope

## Before We Continue

In the previous chapter we learned:

```text
SupervisorJob
```

which introduced a new failure propagation rule:

```text
Child Failure
↓
Failure Stays Local
↓
Siblings Continue
```

A natural question appears:

```text
Where Can We Use This Rule?
```

This chapter answers that question.

Think of:

```text
SupervisorJob
```

as:

```text
The Rule
```

and:

```text
supervisorScope
```

as:

```text
A Structured Scope That Uses The Rule
```

SupervisorJob introduced:

```text
Independent Failure
```

This chapter shows how to apply that behavior while still preserving:

```text
Structured Concurrency

Ownership

Completion Tracking
```

## The Limitation Of coroutineScope

We previously learned:

```text
coroutineScope
```

provides:

```text
Shared Ownership

Shared Completion

Shared Failure
```

Consider:

```kotlin
coroutineScope {

    launch {
        loadUser()
    }

    launch {
        loadFeed()
    }

    launch {
        throw RuntimeException("Boom")
    }
}
```

The result is:

```text
Child Fails
↓
Parent Fails
↓
Remaining Children Cancelled
```

This behavior comes directly from Structured Concurrency.

---

## A Natural Question

After learning about SupervisorJob, a natural question appears:

```text
Can We Have A Structured Scope

Without Shared Failure?
```

In other words:

```text
Can We Keep

Shared Ownership

Shared Completion

But Remove

Shared Failure?
```

This is exactly the problem that:

```text
supervisorScope
```

solves.

---

## Enter supervisorScope

Conceptually:

```text
supervisorScope
```

behaves very similarly to:

```text
coroutineScope
```

Both provide:

```text
Structured Concurrency

Parent-Child Relationships

Shared Ownership

Completion Tracking
```

However, they differ in one important area:

```text
Failure Propagation
```

---

## coroutineScope vs supervisorScope

### coroutineScope

```text
Child Failure
↓
Parent Failure
↓
Sibling Cancellation
```

---

### supervisorScope

```text
Child Failure
↓
Failure Stays Local
↓
Siblings Continue
```

This is the defining behavior of `supervisorScope`.

---

## Example

Consider:

```kotlin
supervisorScope {

    launch {
        loadUser()
    }

    launch {
        loadFeed()
    }

    launch {
        throw RuntimeException("Boom")
    }
}
```

Conceptually:

```text
Parent
├─ Child A
├─ Child B
└─ Child C
```

Suppose:

```text
Child C Fails
```

The result becomes:

```text
Parent
├─ Child A Running
├─ Child B Running
└─ Child C Failed
```

Notice:

```text
Child A
```

and

```text
Child B
```

continue running.

Failure does not automatically spread to sibling coroutines.

---

## What Does supervisorScope Preserve?

A common misconception is:

```text
supervisorScope Makes Everything Independent
```

This is not true.

The following still remain:

```text
Parent-Child Relationships

Ownership

Completion Tracking

Structured Concurrency
```

The structure still exists.

The parent still owns the children.

The parent still waits for child work to finish.

---

## First Mental Model

Think of:

```text
coroutineScope
```

as:

```text
One Team

One Failure
```

If one member fails:

```text
Entire Team Stops
```

---

Think of:

```text
supervisorScope
```

as:

```text
One Team

Independent Failures
```

If one member fails:

```text
Only That Member Stops
```

The rest of the team continues working.

---

## Does supervisorScope Still Wait For Children?

Yes.

This is an important detail.

Consider:

```kotlin
supervisorScope {

    launch {
        loadUser()
    }

    launch {
        loadFeed()
    }
}

println("Done")
```

Just like:

```text
coroutineScope
```

the scope does not finish until all child work has completed.

Conceptually:

```text
Create Children
↓
Wait For Children
↓
Scope Finishes
↓
Continue Execution
```

Failure propagation changes.

Completion behavior does not.

---

## Another Mental Model

Think of:

```text
coroutineScope
```

as:

```text
Shared Completion

Shared Failure
```

---

Think of:

```text
supervisorScope
```

as:

```text
Shared Completion

Independent Failure
```

This is the most important distinction.

---

## What supervisorScope Does NOT Do

A common misconception is:

```text
supervisorScope Handles Exceptions
```

Incorrect.

Just like SupervisorJob:

```text
supervisorScope
```

changes:

```text
Failure Propagation
```

It does not decide:

```text
How Exceptions Are Processed
```

Exception handling is a separate topic.

---

## Why Does supervisorScope Exist?

Sometimes application features are related:

```text
Load User

Load Feed

Load Ads
```

but not equally important.

If:

```text
Load Ads
```

fails,

we may still want:

```text
Load User

Load Feed
```

to succeed.

`supervisorScope` allows related work to remain:

```text
Structured
```

without forcing:

```text
Shared Failure
```

---

## Common Misconceptions

### Misconception 1

```text
supervisorScope Removes Structured Concurrency
```

Incorrect.

Structured Concurrency still exists.

---

### Misconception 2

```text
supervisorScope Does Not Wait For Children
```

Incorrect.

The scope still waits for child work to complete.

---

### Misconception 3

```text
supervisorScope Prevents Failures
```

Incorrect.

Failures still happen.

Only failure propagation changes.

---

### Misconception 4

```text
supervisorScope Handles Exceptions
```

Incorrect.

Exception handling is a separate concern.

---

## The Key Mental Model

```text
coroutineScope
=
Shared Completion

Shared Failure
```

---

```text
supervisorScope
=
Shared Completion

Independent Failure
```

This single difference explains why `supervisorScope` exists.

---

## Revision Table

| Section | Content |
|----------|----------|
| Type | Suspend Function |
| Main Purpose | Structured scope with independent child failures |
| Preserves Ownership? | Yes |
| Preserves Completion Tracking? | Yes |
| Preserves Structured Concurrency? | Yes |
| Changes Failure Propagation? | Yes |
| Child Failure Cancels Siblings? | No |
| Previous Concept | SupervisorJob |
| Next Concept | Dispatchers |

---

## Questions We Are Intentionally Postponing

You may naturally wonder:

```text
How Does supervisorScope Use SupervisorJob?

What Happens To The Parent?

What Happens To Exceptions?

What Happens When Multiple Children Fail?
```

These are excellent questions.

However, they require concepts we have not yet learned:

```text
Coroutine Context

Dispatchers

Exception Handling
```

We will answer them in future chapters.

For this chapter, focus on one idea:

```text
supervisorScope Provides Structured Concurrency Without Shared Failure
```
