# NonCancellable

## A Question From The Previous Chapter

In the previous chapter we learned:

```text
Cancellation Requested
↓
Coroutine Notices
↓
Coroutine Stops
```

A natural question appears:

```text
Should Cancellation Always Win?
```

In many situations:

```text
Yes
```

If work is no longer needed:

```text
Stop The Work
```

However, some situations are different.

---

## A Real Problem

Imagine:

```kotlin
launch {

    openFile()

    processFile()

    closeFile()
}
```

Suppose cancellation happens after:

```text
openFile()
```

and:

```text
processFile()
```

but before:

```text
closeFile()
```

A natural question appears:

```text
Should closeFile() Still Execute?
```

In many situations:

```text
Yes
```

because:

```text
File Opened
↓
File Must Be Closed
```

Otherwise:

```text
Resources May Leak
```

---

## More Real-World Examples

Examples of work that may still need to happen during cancellation:

```text
Close File

Release Camera

Close Network Connection

Release Lock

Save Final State

Complete Database Cleanup
```

These are often:

```text
Cleanup Operations
```

rather than:

```text
Business Logic
```

---

## Revisiting Cancellation

Recall:

```text
Cancellation
```

means:

```text
This Coroutine Is No Longer Needed
```

The important detail is:

```text
Cancellation Does Not Mean Cleanup Is No Longer Needed
```

These are different concerns.

---

## The Problem With Suspending Cleanup

Consider:

```kotlin
launch {

    try {

        processFile()

    } finally {

        delay(1000)

        closeFile()
    }
}
```

Suppose cancellation happens while:

```text
processFile()
```

is running.

---

Execution enters:

```text
finally
```

and reaches:

```kotlin
delay(1000)
```

A very important detail:

```text
delay()
```

is cancellation-aware.

Conceptually:

```text
Cancellation Requested
↓
delay() Notices Cancellation
↓
delay() Stops
```

As a result:

```text
closeFile()
```

may never execute.

---

This is the problem that:

```text
NonCancellable
```

solves.

---

## What Is NonCancellable?

Conceptually:

```text
NonCancellable
```

creates a region where:

```text
Essential Cleanup Work Can Finish
```

even if cancellation has already been requested.

---

Think of:

```text
NonCancellable
```

as:

```text
A Cleanup Zone
```

rather than:

```text
Ignore Cancellation
```

---

## First Mental Model

Think of application shutdown.

Conceptually:

```text
Shutdown Requested
↓
Stop Normal Work
↓
Perform Cleanup
↓
Exit
```

The cleanup phase is where:

```text
NonCancellable
```

is often useful.

---

## Example

```kotlin
launch {

    try {

        processFile()

    } finally {

        withContext(NonCancellable) {

            delay(1000)

            closeFile()
        }
    }
}
```

Conceptually:

```text
Cancellation Requested
↓
Enter finally
↓
Enter NonCancellable
↓
Cleanup Allowed To Finish
↓
Coroutine Ends
```

---

## Timeline Example

```kotlin
launch {

    try {

        println("Work Started")

        delay(5000)

        println("Work Finished")

    } finally {

        withContext(NonCancellable) {

            println("Cleanup Started")

            delay(2000)

            println("Cleanup Finished")
        }
    }
}
```

Suppose:

```kotlin
job.cancel()
```

is called after:

```text
1 Second
```

---

Conceptually:

```text
Work Started
↓
delay(5000)
↓
Cancellation Requested
↓
Enter finally
↓
Cleanup Started
↓
delay(2000)
↓
Cleanup Finished
↓
Coroutine Ends
```

---

Output:

```text
Work Started

Cleanup Started

Cleanup Finished
```

Notice:

```text
Work Finished
```

never appears.

The work was cancelled.

The cleanup was allowed to finish.

---

## What If Cleanup Does Not Suspend?

Consider:

```kotlin
finally {

    closeFile()
}
```

A natural question appears:

```text
Do We Still Need NonCancellable?
```

Usually:

```text
No
```

Simple synchronous cleanup often works without:

```text
NonCancellable
```

because no cancellation-aware suspension points are involved.

---

The need for:

```text
NonCancellable
```

typically appears when cleanup contains:

```text
Suspending Functions
```

such as:

```text
delay()

Network Calls

Database Operations

Other Suspending Work
```

---

## Another Common Misconception

A common misconception is:

```text
NonCancellable = Ignore Cancellation
```

Incorrect.

The coroutine is already cancelled.

Cancellation has already happened.

---

The more accurate mental model is:

```text
Cancellation Happened
↓
Before Exiting
↓
Allow Essential Cleanup To Finish
```

---

## Why Not Use NonCancellable Everywhere?

A natural question appears:

```text
Why Not Put Everything Inside NonCancellable?
```

Consider:

```kotlin
withContext(NonCancellable) {

    downloadMovie()

    syncDatabase()

    refreshCache()

    uploadLogs()
}
```

This effectively ignores the user's request to stop.

---

The purpose of:

```text
Cancellation
```

is:

```text
Stop Unnecessary Work
```

Using:

```text
NonCancellable
```

for large amounts of normal work defeats that purpose.

---

## Intended Usage

Good candidates for:

```text
NonCancellable
```

include:

```text
Close File

Release Lock

Close Database Transaction

Save Final State

Release Resources

Disconnect Network Connection
```

These are usually:

```text
Small Cleanup Tasks
```

that help the application shut down safely.

---

## Is There A Time Limit?

A natural question appears:

```text
How Much Work Can Happen Inside NonCancellable?
```

Technically:

```text
There Is No Built-In Time Limit
```

Kotlin does not automatically stop a:

```text
NonCancellable
```

block after a certain number of seconds.

---

However:

```text
No Technical Limit
```

does not mean:

```text
Unlimited Work Is A Good Idea
```

---

The intended design is:

```text
Small Cleanup
↓
Exit
```

not:

```text
Cancellation Requested
↓
Continue Normal Business Logic For Several Minutes
```

---

## Another Mental Model

Do not think:

```text
NonCancellable = Continue Working
```

Think:

```text
Cancellation Happened
↓
Perform Essential Cleanup
↓
Terminate
```

This is much closer to how:

```text
NonCancellable
```

is intended to be used.

---

## Relationship To Earlier Concepts

Recall:

```text
Cancellation
```

means:

```text
This Coroutine Is No Longer Needed
```

---

```text
Cancellation Checks
```

help detect cancellation.

---

```text
NonCancellable
```

handles the special case where:

```text
Certain Cleanup Must Finish Even After Cancellation
```

---

Conceptually:

```text
Cancellation Requested
↓
Normal Work Stops
↓
Essential Cleanup Finishes
↓
Coroutine Ends
```

---

## The Key Mental Model

```text
NonCancellable
```

does not exist to:

```text
Continue Normal Work
```

It exists to:

```text
Finish Essential Cleanup
```

after cancellation has already occurred.

Understanding this distinction prevents many common mistakes.

---

## Revision Table

| Section | Content |
|----------|----------|
| Type | Cancellation Utility |
| Main Purpose | Allow essential cleanup to finish |
| Ignores Cancellation? | No |
| Cancellation Already Happened? | Yes |
| Typical Usage | Cleanup |
| Common Location | finally Block |
| Good For | Resource Release |
| Good For | Final State Saving |
| Previous Concept | Cancellation Checks |
| Next Concept | Exception Handling |

---

## A Natural Question

So far we have focused on:

```text
Successful Execution

Cancellation
```

A natural question appears:

```text
What Happens
When Something
Goes Wrong?
```

For example:

```text
Network Failure

Database Failure

Unexpected Bug
```

How do coroutines handle these situations?

This question leads us to:

```text
Exception Handling
```

which we will explore next.

---

For this chapter, focus on one idea:

```text
NonCancellable
```

exists to:

```text
Finish Essential Cleanup
```

after cancellation has already occurred.
