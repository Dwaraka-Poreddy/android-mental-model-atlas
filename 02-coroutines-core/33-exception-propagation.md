# Exception Propagation

## A Question From The Previous Chapter

In the previous chapter we learned:

```text
Exceptions Can Be Handled Using try-catch
```

A natural question appears:

```text
What Happens If Nobody Handles The Exception?
```

Does the exception disappear?

Does only the current coroutine fail?

Can other coroutines be affected?

This leads us to:

```text
Exception Propagation
```

---

## A Common Assumption

Many developers initially imagine:

```text
Coroutine Fails
↓
Coroutine Ends
↓
End Of Story
```

This would make sense if coroutines were completely independent.

However, earlier we learned:

```text
Parent Child Hierarchy

Structured Concurrency
```

Coroutines are often connected to one another.

Because of these relationships:

```text
Failure Can Spread Through The Hierarchy
```

---

## Revisiting Parent–Child Relationships

Recall:

```text
Parent Coroutine
↓
launch
↓
Child Coroutine
```

Earlier we learned:

```text
Parent Owns Child
```

---

We learned:

```text
Parent Waits For Child
```

---

We learned:

```text
Parent Can Cancel Child
```

A natural question appears:

```text
Can Child Failure Affect Parent?
```

The answer is:

```text
Yes
```

And understanding this is the purpose of this chapter.

---

## First Example

Consider:

```kotlin
coroutineScope {

    launch {

        throw RuntimeException("Failure")
    }

    launch {

        loadUser()
    }
}
```

A natural question appears:

```text
What Happens To loadUser()?
```

Many developers initially assume:

```text
Nothing
```

because:

```text
Different Coroutine
```

However, that is not what happens.

---

## What Actually Happens?

Conceptually:

```text
Child A
↓
Throws Exception
```

---

Then:

```text
Parent Learns About The Failure
```

---

Then:

```text
Parent Fails
```

---

Then:

```text
Other Children Are Cancelled
```

Conceptually:

```text
Child A Failure
↓
Parent Notified
↓
Parent Fails
↓
Child B Cancelled
```

This process is called:

```text
Exception Propagation
```

---

## First Mental Model

Think of propagation as:

```text
Failure Information Moving Through The Hierarchy
```

---

Not:

```text
Random Failure Appearing Everywhere
```

---

Conceptually:

```text
Child Reports Failure
↓
Parent Learns About Failure
```

That is much closer to what actually happens.

---

## Why Does Kotlin Behave This Way?

A natural question appears:

```text
Why Doesn't
The Failure Stay
Inside The Child?
```

Because:

```text
Structured Concurrency
```

treats a group of coroutines as:

```text
One Unit Of Work
```

rather than:

```text
Independent Islands
```

---

## House Construction Analogy

Imagine:

```text
Build House
```

is the parent task.

---

Child A:

```text
Build Foundation
```

---

Child B:

```text
Build Roof
```

Suppose:

```text
Foundation Fails
```

A natural question appears:

```text
Should We Continue Building The Roof?
```

Usually:

```text
No
```

The larger task has already failed.

---

Kotlin applies a similar philosophy.

---

Conceptually:

```text
Child Failure
↓
Parent Failure
↓
Remaining Work Cancelled
```

---

## Another Example

Consider:

```kotlin
coroutineScope {

    launch {

        syncDatabase()
    }

    launch {

        throw RuntimeException()
    }

    launch {

        uploadAnalytics()
    }
}
```

Conceptually:

```text
Coroutine 1 Running
Coroutine 2 Fails
Coroutine 3 Running
```

---

Then:

```text
Failure Reaches Parent
↓
Parent Fails
↓
Remaining Coroutines Cancelled
```

The entire group is treated as failed.

---

## Another Mental Model

Think of:

```text
coroutineScope
```

as a team.

Conceptually:

```text
Team
├─ Member A
├─ Member B
└─ Member C
```

If one member reports:

```text
Critical Failure
```

the team may decide:

```text
Stop The Entire Operation
```

That is very similar to normal exception propagation.

---

## Another Common Misconception

A common misconception is:

```text
Exception Propagation Means The Exception Disappears
```

Incorrect.

---

The exception is still present.

The important idea is:

```text
More Coroutines Become Aware Of The Failure
```

---

## Another Common Misconception

A common misconception is:

```text
Only The Failing Coroutine Is Affected
```

Incorrect.

Because of:

```text
Parent Child Relationships
```

other coroutines may be affected as well.

---

## Relationship To Structured Concurrency

Earlier we learned:

```text
Structured Concurrency
```

creates ownership relationships.

This chapter reveals an important consequence.

Those relationships affect:

```text
Success

Cancellation

Failure
```

---

Conceptually:

```text
Success Flows Through Hierarchy
```

---

```text
Cancellation Flows Through Hierarchy
```

---

```text
Failure Flows Through Hierarchy
```

All three behaviors are connected to the same hierarchy.

---

## Relationship To SupervisorJob

A natural question appears:

```text
What If I Don't Want Child Failure To Affect Other Children?
```

Interesting.

Earlier we learned:

```text
SupervisorJob

supervisorScope
```

These concepts exist specifically because there are situations where normal exception propagation is not desirable.

We will reconnect these ideas later.

For now, focus on understanding normal propagation behavior.

---

## The Key Mental Model

Exception propagation means:

```text
Unhandled Failure Does Not Stay Isolated
```

Instead:

```text
Child Failure
↓
Parent Learns About It
↓
Failure Moves Through The Hierarchy
```

This behavior is a direct consequence of:

```text
Structured Concurrency
```

and:

```text
Parent Child Relationships
```

---

## Revision Table

| Section | Content |
|----------|----------|
| Type | Failure Propagation Concept |
| Main Purpose | Explain where unhandled exceptions go |
| Trigger | Unhandled Exception |
| First Effect | Parent Learns About Failure |
| Typical Outcome | Parent Fails |
| Typical Outcome | Other Children Cancelled |
| Related To | Parent Child Hierarchy |
| Related To | Structured Concurrency |
| Related To | SupervisorJob |
| Previous Concept | Exception Handling |
| Next Concept | CoroutineExceptionHandler |

---

## A Natural Question

We now understand:

```text
Unhandled Exceptions Move Through The Hierarchy
```

A natural question appears:

```text
Who Eventually Handles The Failure?
```

At some point, somebody must observe the exception.

How does Kotlin provide a place to observe unhandled coroutine failures?

This question leads us to:

```text
CoroutineExceptionHandler
```

which we will explore next.

---

For this chapter, focus on one idea:

```text
Exception Propagation
```

means:

```text
Unhandled Failure Does Not Stay Isolated
```

Instead:

```text
Failure Information Moves Through The Coroutine Hierarchy
```
