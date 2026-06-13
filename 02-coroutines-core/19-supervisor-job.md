# SupervisorJob

## A New Dimension: Failure

So far we have discussed:

```text
Creation

Ownership

Completion
```

We have learned:

```text
CoroutineScope

Job

Parent-Child Jobs

Structured Concurrency

coroutineScope
```

These concepts help us answer questions such as:

```text
Who Owns A Coroutine?

Who Waits For It?

When Is Work Finished?
```

However, an important question remains:

```text
What Happens If A Child Fails?
```

---

## A Natural Assumption

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

A natural assumption is:

```text
Child 3 Fails

Child 1 Continues

Child 2 Continues
```

Many developers expect failure to remain local to the coroutine that failed.

However, that is NOT what happens.

---

## The Default Rule

With normal parent-child jobs:

```text
Child Fails
↓
Failure Propagates To Parent
↓
Parent Cancels Remaining Children
```

Conceptually:

```text
Parent
├─ Child A
├─ Child B
└─ Child C (Fails)
```

becomes:

```text
Parent Cancelled
├─ Child A Cancelled
├─ Child B Cancelled
└─ Child C Failed
```

---

## Why Does Kotlin Behave This Way?

This behavior is a consequence of Structured Concurrency.

Remember:

```text
Structured Concurrency
```

treats related work as:

```text
One Structure

One Lifecycle
```

If part of that structure fails:

```text
The Structure Is Considered Failed
```

Failure is treated as a shared concern.

---

## First Mental Model

Think of normal parent-child jobs as:

```text
One Team
```

If one critical member fails:

```text
Team Fails
```

Conceptually:

```text
Child Failure
↓
Shared Failure
```

This is the default coroutine behavior.

---

## Is Shared Failure Always Desirable?

Not necessarily.

Consider:

```text
Load User

Load Feed

Load Ads
```

Suppose:

```text
Load Ads
```

fails.

Should:

```text
Load User

Load Feed
```

also be cancelled?

Maybe not.

The application may still be able to function without advertisements.

---

## The Real Problem

Sometimes we want:

```text
Shared Ownership
```

but:

```text
Independent Failure
```

We still want:

```text
Parent
├─ Child A
├─ Child B
└─ Child C
```

However:

```text
Child C Fails
```

should not automatically mean:

```text
Child A Cancelled

Child B Cancelled
```

This is the problem that:

```text
SupervisorJob
```

was created to solve.

---

## Enter SupervisorJob

Conceptually:

```text
SupervisorJob
```

changes the failure rule.

Normal Job:

```text
Child Failure
↓
Parent Failure
↓
Sibling Cancellation
```

SupervisorJob:

```text
Child Failure
↓
Failure Stays Local
↓
Siblings Continue
```

---

## Second Mental Model

Think of:

```text
Job
```

as:

```text
All For One
```

If one child fails:

```text
Everyone Stops
```

---

Think of:

```text
SupervisorJob
```

as:

```text
Independent Workers
```

If one child fails:

```text
Only That Child Stops
```

The remaining children continue.

---

## Example

Conceptually:

```text
Supervisor
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
Supervisor
├─ Child A Running
├─ Child B Running
└─ Child C Failed
```

Notice:

```text
Failure Did Not Spread
```

This is the defining behavior of SupervisorJob.

---

## What Does SupervisorJob Change?

SupervisorJob changes:

```text
Failure Propagation
```

It does NOT change:

```text
Ownership

Parent-Child Relationships

Lifecycle Tracking
```

The structure still exists.

The parent still owns the children.

The difference is:

```text
Failure Is No Longer Contagious
```

---

## Important Clarification

SupervisorJob does NOT mean:

```text
Failures Disappear
```

Failures still happen.

Exceptions still occur.

The difference is:

```text
Failure Does Not Automatically Cancel Sibling Coroutines
```

---

## Another Mental Model

Normal Job:

```text
One Child Falls
↓
Entire Structure Falls
```

SupervisorJob:

```text
One Child Falls
↓
Only That Child Falls
```

---

## What SupervisorJob Does NOT Solve

A common misconception is:

```text
SupervisorJob Handles Exceptions
```

Incorrect.

SupervisorJob controls:

```text
How Failure Spreads
```

It does NOT decide:

```text
How Failures Are Handled
```

Exception handling is a separate topic that we will discuss later.

---

## Common Misconceptions

### Misconception 1

```text
SupervisorJob Prevents Failures
```

Incorrect.

Failures still happen.

SupervisorJob only changes how those failures affect other coroutines.

---

### Misconception 2

```text
SupervisorJob Removes Parent-Child Relationships
```

Incorrect.

The hierarchy still exists.

Ownership still exists.

Lifecycle tracking still exists.

---

### Misconception 3

```text
SupervisorJob Ignores Exceptions
```

Incorrect.

Exceptions still occur.

SupervisorJob only changes failure propagation.

---

### Misconception 4

```text
SupervisorJob Means Every Coroutine Becomes Independent
```

Incorrect.

Coroutines still belong to the same structure.

The change is specifically about:

```text
Failure Propagation
```

---

## The Key Mental Model

Normal Job:

```text
Shared Ownership

Shared Failure
```

SupervisorJob:

```text
Shared Ownership

Independent Failure
```

This single idea explains why SupervisorJob exists.

---

## Revision Table

| Section | Content |
|----------|----------|
| Type | Job |
| Main Purpose | Prevent child failures from automatically cancelling sibling coroutines |
| Changes Ownership? | No |
| Changes Lifecycle Tracking? | No |
| Changes Failure Propagation? | Yes |
| Default Job Rule | Failure Spreads |
| SupervisorJob Rule | Failure Stays Local |
| Previous Concept | runBlocking |
| Next Concept | supervisorScope |

---

## Questions We Are Intentionally Postponing

You may naturally wonder:

```text
How Do We Create A SupervisorJob?

How Is It Used With CoroutineScope?

What Happens To The Parent?

What Happens To Exceptions?
```

These are excellent questions.

However, they require concepts we have not yet learned:

```text
supervisorScope

Exception Handling
```

We will answer them in future chapters.

For this chapter, focus on one idea:

```text
SupervisorJob Allows Coroutines To Share Ownership

Without Sharing Failure
```
