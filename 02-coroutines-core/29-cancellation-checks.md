# Cancellation Checks

## A Question From The Previous Chapter

In the previous chapter we learned:

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

We also learned:

```text
Cancellation Is Cooperative
```

A natural question appears:

```text
How Does A Coroutine Know It Has Been Cancelled?
```

This question leads us to:

```text
Cancellation Checks
```

---

## Revisiting Cooperative Cancellation

Recall:

```text
Cancellation Requested
↓
Coroutine Notices
↓
Coroutine Stops
```

A natural question appears:

```text
How Does The Coroutine Notice?
```

Some mechanism must exist that allows a coroutine to detect:

```text
A Cancellation Request
```

Kotlin provides multiple ways to do this.

The two most common are:

```text
isActive

ensureActive
```

---

## First Mental Model

Think of cancellation checks as:

```text
Periodic Checkpoints
```

where a coroutine asks:

```text
Should I Continue?
```

Conceptually:

```text
Continue?
↓
Yes
↓
Keep Working
```

or:

```text
Continue?
↓
No
↓
Stop Working
```

---

## The First Tool: isActive

Conceptually:

```text
isActive
```

answers:

```text
Is This Coroutine Still Allowed To Continue Running?
```

---

Think of:

```text
isActive
```

as:

```text
A Question
```

---

Example:

```kotlin
launch {

    while (isActive) {

        processData()
    }
}
```

Conceptually:

```text
Check isActive
↓
True
↓
Continue
```

---

Later:

```text
Cancellation Requested
↓
Check isActive
↓
False
↓
Stop
```

---

## What Does isActive Give Us?

isActive provides:

```text
Information
```

The coroutine can then decide:

```text
What To Do With That Information
```

For example:

```kotlin
if (!isActive) {
    return
}
```

or:

```kotlin
if (!isActive) {

    saveProgress()

    return
}
```

or:

```kotlin
if (!isActive) {

    logCancellation()

    return
}
```

The coroutine remains in control.

---

## A Natural Question

Suppose:

```kotlin
if (!isActive) {
    return
}
```

appears throughout the codebase.

A natural question appears:

```text
What If I Simply Want Normal Cancellation Behavior?
```

Meaning:

```text
If Cancellation Happened

Stop Immediately
```

without writing custom logic every time.

This question leads us to:

```text
ensureActive
```

---

## The Second Tool: ensureActive

Conceptually:

```text
ensureActive
```

also checks:

```text
Has Cancellation Been Requested?
```

However, it behaves differently.

Instead of:

```text
Returning Information
```

it:

```text
Immediately Reacts To Cancellation
```

using Kotlin's standard cancellation mechanism.

---

Example:

```kotlin
ensureActive()
```

Conceptually:

```text
Cancelled?
↓
No
↓
Continue
```

---

```text
Cancelled?
↓
Yes
↓
Stop Immediately
```

---

## What Does ensureActive Give Us?

ensureActive provides:

```text
Enforcement
```

rather than:

```text
Information
```

Think of:

```text
isActive
```

as:

```text
Tell Me Whether Cancellation Happened
```

---

Think of:

```text
ensureActive
```

as:

```text
Check Whether Cancellation Happened

And Use Kotlin's Built-In Cancellation Mechanism If It Did
```

We will explore the exact mechanics of Kotlin's cancellation mechanism in a later chapter.

For now, focus on:

```text
isActive
↓
Provides Information
```

---

```text
ensureActive
↓
Immediately Reacts To Cancellation
```

---

## Why Do Both APIs Exist?

A natural question appears:

```text
If Both Check For Cancellation,

Why Do We Need Both?
```

Because they solve slightly different needs.

---

Sometimes the coroutine wants:

```text
Custom Behavior
```

Examples:

```text
Save Progress

Log Information

Perform Cleanup

Return Custom Result
```

In these situations:

```text
isActive
```

is often useful.

---

Other times the coroutine simply wants:

```text
Normal Cancellation Behavior
```

In those situations:

```text
ensureActive
```

may be more convenient.

---

## Another Mental Model

Think of:

```text
isActive
```

as:

```text
Ask A Question
```

---

Think of:

```text
ensureActive
```

as:

```text
Perform A Verification
```

The first returns information.

The second enforces a response.

---

## Example Comparison

Using:

```kotlin
if (!isActive) {
    return
}
```

Conceptually:

```text
Check State
↓
Developer Decides What Happens Next
```

---

Using:

```kotlin
ensureActive()
```

Conceptually:

```text
Check State
↓
Use Standard Cancellation Behavior
```

---

## When Is isActive Useful?

Common examples:

```text
Loops

Polling

Background Monitoring

Continuous Processing
```

These situations often require:

```text
Custom Decisions
```

based on cancellation state.

---

## Production Example

Imagine a ViewModel that continuously refreshes data.

```kotlin
viewModelScope.launch {

    while (isActive) {

        repository.refreshData()

        delay(5000)
    }
}
```

Conceptually:

```text
Refresh Data
↓
Wait 5 Seconds
↓
Refresh Again
```

Suppose the user leaves the screen and the ViewModel is cleared.

Conceptually:

```text
Cancellation Requested
↓
isActive Becomes False
↓
Loop Stops
```

This is one of the most common real-world uses of:

```text
isActive
```

in Android applications.

---

## When Is ensureActive Useful?

Common examples:

```text
Important Checkpoints

Long Computations

Validation Boundaries

Before Expensive Operations
```

These situations often require:

```text
Immediate Cancellation If Cancellation Was Requested
```

---

## Which One Should I Use?

A natural question appears:

```text
When Should I Use isActive

And When Should I Use ensureActive?
```

A useful rule of thumb is:

```text
Need Custom Logic?
↓
Use isActive
```

Examples:

```text
Save Progress

Log Information

Perform Cleanup

Return Early
```

---

```text
Need Normal Cancellation Handling?
↓
Use ensureActive
```

Examples:

```text
Long Computations

Validation Checkpoints

Expensive Processing Steps
```
--- 

In practice:

```text
isActive
```

is commonly seen inside loops.

---

```text
ensureActive
```

is commonly seen at important checkpoints in long-running work.

---

## Another Common Misconception

A common misconception is:

```text
isActive Cancels The Coroutine
```

Incorrect.

It only:

```text
Reports State
```

---

Cancellation is still requested elsewhere.

---

## Another Common Misconception

A common misconception is:

```text
ensureActive Requests Cancellation
```

Incorrect.

Cancellation must already have been requested.

ensureActive simply:

```text
Responds To Existing Cancellation
```

---

## Another Common Misconception

A common misconception is:

```text
ensureActive Is Better Than isActive
```

Incorrect.

Neither is universally better.

They provide different levels of control.

---

## Revisiting Job

Earlier we learned:

```text
Job
```

tracks coroutine lifecycle.

When cancellation occurs:

```text
Job State Changes
```

Both:

```text
isActive

ensureActive
```

ultimately observe that lifecycle state.

Conceptually:

```text
Job
↓
Cancellation State
↓
Cancellation Checks
```

---

## Comparison Table

| Question | isActive | ensureActive |
|-----------|-----------|-----------|
| Checks Cancellation? | Yes | Yes |
| Returns Information? | Yes | No |
| Lets You Decide What To Do? | Yes | Limited |
| Automatically Reacts To Cancellation? | No | Yes |
| Typical Use | Loops, polling, custom logic | Checkpoints, validations, expensive work boundaries |

---

## The Key Mental Model

Both:

```text
isActive

ensureActive
```

exist to answer:

```text
Has Cancellation Been Requested?
```

The difference is:

```text
isActive
↓
Provides Information
```

---

```text
ensureActive
↓
Enforces Response
```

Understanding this distinction explains why Kotlin provides both APIs.

---

## Revision Table

| Section | Content |
|----------|----------|
| Type | Cancellation Utilities |
| Main Purpose | Detect cancellation |
| First Tool | isActive |
| Second Tool | ensureActive |
| isActive Returns Information? | Yes |
| ensureActive Returns Information? | No |
| isActive Allows Custom Behavior? | Yes |
| ensureActive Uses Standard Cancellation Behavior? | Yes |
| Previous Concept | Cancellation |
| Next Concept | NonCancellable |

---

## A Natural Question

We now understand:

```text
How A Coroutine Detects Cancellation
```

A natural question appears:

```text
What If Certain Work Must Complete

Even After Cancellation?
```

For example:

```text
Cleanup

Resource Release

Final State Saving
```

Should cancellation stop that work as well?

This question leads us to:

```text
NonCancellable
```

which we will explore next.

---

For this chapter, focus on one idea:

```text
isActive
```

and:

```text
ensureActive
```

both detect cancellation.

The difference is:

```text
isActive
↓
Provides Information
```

while:

```text
ensureActive
↓
Enforces Response
```
