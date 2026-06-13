# Cancellation

## The Problem

So far we have learned:

```text
How Coroutines Start

How Coroutines Suspend

How Coroutines Resume

How Coroutines Are Scheduled
```

In almost every example, the coroutine looked like this:

```text
Start
↓
Do Work
↓
Finish
```

A natural assumption appears:

```text
Once A Coroutine Starts It Will Eventually Finish
```

However, real applications are not that simple.

---

## A Real-World Problem

Imagine:

```text
User Opens Screen
↓
Load User Data
```

A coroutine starts:

```kotlin
launch {

    loadUser()
}
```

Now suppose:

```text
User Leaves Screen
```

before:

```text
loadUser()
```

finishes.

A natural question appears:

```text
Should The CoroutinexKeep Running?
```

---

## Another Example

Suppose:

```text
Download Starts
```

and the user presses:

```text
Cancel
```

A natural question appears:

```text
Should The Download Continue Anyway?
```

---

## Another Example

Suppose:

```text
Search Request Starts
```

and before it finishes:

```text
User Types A New Search
```

A natural question appears:

```text
Should The Old Search Still Continue?
```

---

## A New Requirement Appears

So far we have focused on:

```text
Starting Coroutines
```

Real applications also need a way to:

```text
Stop Coroutines
```

This is the problem that:

```text
Cancellation
```

solves.

---

## What Is Cancellation?

Conceptually:

```text
Cancellation
```

means:

```text
Requesting That A Coroutine Stop Its Work
```

Think of cancellation as:

```text
The Coroutine Is No Longer Needed
```

---

## Important Clarification

A common misconception is:

```text
Cancellation = Failure
```

Incorrect.

Cancellation is often:

```text
Normal Application Behavior
```

Examples:

```text
User Leaves Screen

User Cancels Operation

Result No Longer Needed
```

None of these situations represent errors.

---

## Revisiting Job

Earlier we learned:

```text
Job
```

represents:

```text
Coroutine Lifecycle
```

Conceptually:

```text
Coroutine
↓
Job
↓
Lifecycle State
```

However, a Job is not only:

```text
Lifecycle Information
```

It is also:

```text
A Cancellation Handle
```

---

## First Mental Model

Think of:

```text
Job
```

as:

```text
The Remote Control For A Coroutine
```

The coroutine performs work.

The Job allows lifecycle operations such as:

```text
Cancellation
```

---

## Example

```kotlin
val job = launch {

    loadUser()
}
```

Conceptually:

```text
Coroutine Running
↓
Job Exists
```

The Job can later be used to interact with that coroutine.

---

## Cancelling A Coroutine

Conceptually:

```text
Coroutine Running
↓
Cancellation Requested
↓
Coroutine Stops
```

This is the essence of cancellation.

---

## Why Is Cancellation Important?

Without cancellation:

```text
Unused Coroutines May Continue Running
```

Examples:

```text
Screen No Longer Visible

Result No Longer Needed

User Requested Stop
```

Continuing work in these situations wastes:

```text
CPU

Memory

Battery

Network Resources
```

---

## Cancellation And Structured Concurrency

Earlier we learned:

```text
Structured Concurrency
```

and:

```text
Parent Child Job Hierarchy
```

These concepts become more important once cancellation exists.

A natural question appears:

```text
If A Parent Stops,

What Happens To Its Children?
```

We will explore this behavior in future chapters.

For now, remember:

```text
Cancellation And Job Hierarchies Are Closely Related
```

---

## Cancellation Is Cooperative

At this point, a natural question appears:

```text
If A Coroutine Is Cancelled

Does It Instantly Stop?
```

The answer is:

```text
Not Necessarily
```

Cancellation in Kotlin is:

```text
Cooperative
```

This means the coroutine must cooperate with the cancellation process.

We will explore this idea in upcoming chapters.

---

## Why Doesn't Kotlin Forcefully Stop Coroutines?

A natural question appears:

```text
Why Not Simply Kill The Coroutine Immediately?
```

Because abrupt termination can leave work in an inconsistent state.

For example:

```text
File Being Written

Database Being Updated

Cleanup Still Running
```

Stopping execution at an arbitrary instruction may be unsafe.

This is one reason cancellation is cooperative.

---

## Another Mental Model

Think of cancellation as:

```text
Stop Requested
```

rather than:

```text
Forcefully Destroyed
```

Conceptually:

```text
Cancellation Requested
↓
Coroutine Notices
↓
Coroutine Stops
```

The details of how a coroutine notices cancellation will be covered later.

---

## Another Common Misconception

A common misconception is:

```text
Cancelled = Completed
```

Incorrect.

A coroutine can end because:

```text
Work Finished
```

or because:

```text
Cancellation Occurred
```

These are different outcomes.

---

## Another Common Misconception

A common misconception is:

```text
Cancellation Only Matters For Long Tasks
```

Incorrect.

Any coroutine may eventually become:

```text
Unnecessary
```

and cancellation provides a way to stop unnecessary work.

---

## The Relationship To Earlier Concepts

Recall:

```text
CoroutineScope
```

owns coroutines.

---

```text
Job
```

tracks lifecycle.

---

```text
Structured Concurrency
```

organizes coroutine relationships.

---

Cancellation builds on all of these concepts.

Conceptually:

```text
CoroutineScope
↓
Owns Coroutine

Job
↓
Tracks Lifecycle

Cancellation
↓
Stops Lifecycle
```

---

## The Key Mental Model

A coroutine does not always need to run until completion.

Sometimes:

```text
User Leaves Screen

User Cancels Operation

Result Becomes Irrelevant
```

In those situations:

```text
Cancellation
```

provides a way to stop unnecessary work.

---

## Revision Table

| Section | Content |
|----------|----------|
| Type | Coroutine Lifecycle Concept |
| Main Purpose | Stop unnecessary coroutine work |
| Related To | Job |
| Related To | Structured Concurrency |
| Always An Error? | No |
| Forceful? | No |
| Cooperative? | Yes |
| Previous Concept | Dispatcher Internals |
| Next Concept | isActive |

---

## A Natural Question

We now understand:

```text
Cancellation
```

requests that a coroutine stop.

A natural question appears:

```text
How Does A Coroutine Know It Has Been Cancelled?
```

This question leads us to:

```text
isActive
```

which we will explore next.

---

For this chapter, focus on one idea:

```text
Cancellation
```

means:

```text
This Coroutine Is No Longer Needed
```

and therefore:

```text
Its Work Should Stop
```
