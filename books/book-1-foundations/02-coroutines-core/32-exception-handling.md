# Exception Handling

## A Question From The Previous Chapter

In the previous chapter we learned:

```text
CancellationException
```

can cause a coroutine to leave its current execution path.

A natural question appears:

```text
What Happens When A Coroutine Fails?
```

For example:

```text
Network Failure

Database Failure

Unexpected Bug
```

How can a coroutine respond to these failures?

This leads us to:

```text
Exception Handling
```

---

## Three Possible Outcomes

A coroutine can end in three major ways.

---

Outcome 1:

```text
Success
```

Conceptually:

```text
Coroutine Starts
↓
Work Completes
↓
Coroutine Ends
```

---

Outcome 2:

```text
Cancellation
```

Conceptually:

```text
Coroutine Starts
↓
Cancellation Requested
↓
Coroutine Ends
```

---

Outcome 3:

```text
Failure
```

Conceptually:

```text
Coroutine Starts
↓
Exception Thrown
↓
Coroutine Fails
```

This chapter focuses on:

```text
Failure
```

---

## A Simple Example

Consider:

```kotlin
launch {

    loadUser()

    throw RuntimeException("Network Failed")

    loadFeed()
}
```

A natural question appears:

```text
What Happens To loadFeed()?
```

---

Answer:

```text
It Never Executes
```

Conceptually:

```text
loadUser()
↓
Exception Thrown
↓
Current Execution Path Ends
```

Once the exception occurs:

```text
Remaining Work Is Skipped
```

---

## Revisiting Exceptions

Earlier we learned:

```text
Exceptions
```

can change:

```text
Control Flow
```

Conceptually:

```text
Normal Execution
↓
Exception Thrown
↓
Leave Current Path
```

This is exactly what happened in the previous example.

---

## Failure Versus Cancellation

A very important distinction:

```text
Failure
```

and:

```text
Cancellation
```

are not the same thing.

---

Cancellation usually means:

```text
Stop Requested
```

Examples:

```text
User Left Screen

ViewModel Cleared

Result No Longer Needed
```

---

Failure usually means:

```text
Something Unexpected Occurred
```

Examples:

```text
Network Failure

Database Failure

Programming Bug
```

---

Conceptually:

```text
Cancellation
↓
Expected Lifecycle Event
```

---

```text
Failure
↓
Unexpected Problem
```

Understanding this distinction is extremely important.

---

## First Mental Model

Think of failure as:

```text
The Coroutine Cannot Continue
```

because something went wrong.

Conceptually:

```text
Work Starts
↓
Exception Thrown
↓
Remaining Work Skipped
↓
Coroutine Fails
```

---

## A Natural Question

Suppose:

```kotlin
launch {

    loadUser()

    throw RuntimeException()

    loadFeed()
}
```

A natural question appears:

```text
Can We Recover From The Failure?
```

This introduces:

```text
try-catch
```

---

## Enter try-catch

Conceptually:

```text
try-catch
```

provides an opportunity to:

```text
Handle Failure
```

instead of immediately abandoning the operation.

---

Think of:

```text
try
```

as:

```text
Attempt The Work
```

---

Think of:

```text
catch
```

as:

```text
Handle The Failure
```

---

Conceptually:

```text
Attempt Work
↓
Success
↓
Continue
```

or:

```text
Attempt Work
↓
Failure
↓
Handle Failure
```

---

## Minimal Example

```kotlin
launch {

    try {

        loadUser()

    } catch (e: Exception) {

        showError()
    }
}
```

Conceptually:

```text
loadUser()
↓
Exception Thrown
↓
catch Block Executes
↓
Handle Failure
```

---

Instead of:

```text
Coroutine Fails Immediately
```

the coroutine gets an opportunity to respond.

---

## What Does Recovery Mean?

A common misconception is:

```text
try-catch Makes The Exception Disappear
```

Incorrect.

The exception still happened.

---

Conceptually:

```text
Work
↓
Exception Occurred
↓
catch Block Handles It
```

The failure was:

```text
Handled
```

not:

```text
Prevented
```

---

This distinction is important.

---

## Real Android Example

Imagine:

```kotlin
viewModelScope.launch {

    try {

        val user = repository.loadUser()

        updateUi(user)

    } catch (e: Exception) {

        showErrorUi()
    }
}
```

Conceptually:

```text
Load User
↓
Success?
```

---

If successful:

```text
Update UI
```

---

If failure occurs:

```text
Show Error UI
```

This is one of the most common exception handling patterns in Android applications.

---

## Another Example

Consider:

```kotlin
launch {

    try {

        val result = api.fetchData()

        saveResult(result)

    } catch (e: Exception) {

        logFailure()
    }
}
```

Conceptually:

```text
Fetch Data
↓
Success?
├─ Yes → Save Result
└─ No  → Log Failure
```

The coroutine remains in control of how it responds.

---

## Another Common Misconception

A common misconception is:

```text
try-catch Prevents Exceptions
```

Incorrect.

Exceptions still occur.

---

try-catch simply provides a place to:

```text
Respond To Them
```

---

## Another Common Misconception

A common misconception is:

```text
If A catch Block Exists

Nothing Failed
```

Incorrect.

Failure still occurred.

The difference is:

```text
Failure Was Handled
```

instead of:

```text
Failure Was Ignored
```

---

## Another Common Misconception

A common misconception is:

```text
Cancellation And Failure Mean The Same Thing
```

Incorrect.

---

Cancellation means:

```text
Stop Requested
```

---

Failure means:

```text
Something Went Wrong
```

These situations should be viewed differently.

---

## Relationship To Earlier Concepts

Recall:

```text
CancellationException
```

introduced the idea that exceptions can end the current execution path.

---

This chapter extends that idea.

Conceptually:

```text
Exception Thrown
↓
Current Execution Path Ends
```

Whether the exception represents:

```text
Cancellation
```

or:

```text
Failure
```

the mechanism is similar.

---

## The Key Mental Model

Failure is one of the three major coroutine outcomes:

```text
Success

Cancellation

Failure
```

When failure occurs:

```text
try-catch
```

provides an opportunity to:

```text
Handle The Failure
```

instead of immediately abandoning the operation.

---

## Revision Table

| Section | Content |
|----------|----------|
| Type | Failure Handling Concept |
| Main Purpose | Handle coroutine failures |
| Outcome 1 | Success |
| Outcome 2 | Cancellation |
| Outcome 3 | Failure |
| Primary Tool | try-catch |
| Does try-catch Prevent Failure? | No |
| Does try-catch Handle Failure? | Yes |
| Previous Concept | CancellationException |
| Next Concept | Exception Propagation |

---

## A Natural Question

We now understand:

```text
How To Handle A Failure
```

A natural question appears:

```text
What Happens If Nobody Handles The Exception?
```

Does it disappear?

Does the coroutine simply stop?

Can other coroutines be affected?

This question leads us to:

```text
Exception Propagation
```

which we will explore next.

---

For this chapter, focus on one idea:

```text
Failure
```

is a third possible coroutine outcome alongside:

```text
Success

Cancellation
```

and:

```text
try-catch
```

provides a way to respond when that failure occurs.
