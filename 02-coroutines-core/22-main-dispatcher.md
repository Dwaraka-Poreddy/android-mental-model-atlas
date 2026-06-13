# Main Dispatcher

## The Problem

In the previous chapter we learned:

```text
Coroutine
↓
Dispatcher
↓
Thread
```

A dispatcher decides:

```text
Which Thread Executes A Coroutine
```

However, a new question immediately appears:

```text
Which Thread Should Be Chosen?
```

For some work:

```text
Any Thread May Be Fine
```

But not all work has that flexibility.

---

## A Reminder From Earlier Chapters

Back in Doc 1 we learned:

```text
Android UI Is Not Thread Safe
```

This means:

```text
UI Updates Must Happen On The Main Thread
```

For example:

```text
Update Text

Show Loading Spinner

Display User Data

Navigate To Another Screen
```

All of these are UI operations.

---

## A New Problem Appears

Suppose:

```kotlin
launch {

    updateUI()
}
```

A natural question appears:

```text
Which Thread Should Run updateUI()?
```

The answer is:

```text
Main Thread
```

But how does a coroutine express that requirement?

This is the problem that:

```text
Main Dispatcher
```

solves.

---

## Enter Main Dispatcher

Conceptually:

```text
Dispatchers.Main
```

means:

```text
Run This Coroutine On The Main Thread
```

Think of:

```text
Dispatchers.Main
```

as:

```text
The UI Dispatcher
```

Its job is to ensure that coroutine execution happens on the application's main thread.

---

## First Mental Model

Remember:

```text
Coroutine
```

represents:

```text
Work
```

and:

```text
Dispatcher
```

represents:

```text
Thread Selection
```

Therefore:

```text
Dispatchers.Main
```

means:

```text
This Work Requires The Main Thread
```

---

## Example

```kotlin
launch(Dispatchers.Main) {

    updateUI()
}
```

Conceptually:

```text
Coroutine
↓
Main Dispatcher
↓
Main Thread
↓
updateUI()
```

The dispatcher ensures that the coroutine executes on the main thread.

---

## Why Does Android Need This?

Android has a special thread:

```text
Main Thread
```

that is responsible for:

```text
Drawing UI

Handling Touch Events

Running UI Logic
```

Earlier we learned:

```text
MessageQueue

Looper

Main Thread
```

These components work together to keep the UI responsive.

Because the UI system is not thread-safe:

```text
Only The Main Thread Should Modify UI
```

---

## What Happens Without Main Dispatcher?

Imagine:

```text
Background Thread
↓
Update UI
```

This can lead to:

```text
Crashes

Race Conditions

Unexpected Behavior
```

Android protects against this by requiring UI updates to happen on the main thread.

---

## Revisiting Suspension

Consider:

```kotlin
launch(Dispatchers.Main) {

    delay(1000)

    updateUI()
}
```

A natural question appears:

```text
The Coroutine Suspended.

Will It Return To The Main Thread?
```

The answer is:

```text
Yes
```

Because:

```text
Main Dispatcher
```

continues enforcing its thread selection rule.

Conceptually:

```text
Coroutine Starts On Main Thread
↓
Suspends
↓
Resumes
↓
Main Thread Again
```

---

## Why Is This Important?

Imagine:

```kotlin
launch(Dispatchers.Main) {

    delay(1000)

    updateUI()
}
```

After suspension:

```text
updateUI()
```

must still execute safely.

The Main Dispatcher guarantees:

```text
UI Work Remains On The Main Thread
```

even after suspension and resumption.

---

## Another Mental Model

Think of:

```text
Dispatchers.Main
```

as a rule:

```text
No Matter When This Coroutine Runs Use The Main Thread
```

Whether the coroutine:

```text
Starts

Suspends

Resumes
```

the dispatcher continues enforcing that rule.

---

## What Main Dispatcher Does NOT Mean

A common misconception is:

```text
Main Dispatcher Makes Work Faster
```

Incorrect.

Main Dispatcher is about:

```text
Correctness
```

not:

```text
Performance
```

Its purpose is to ensure that UI-related work executes on the correct thread.

---

## Another Common Misconception

A common misconception is:

```text
All Coroutines Should Use Main Dispatcher
```

Incorrect.

Many tasks should NOT run on the main thread.

Examples:

```text
Network Requests

Database Queries

Large File Operations

Heavy Computation
```

Running these on the main thread can cause:

```text
UI Freeze

Jank

ANRs
```

We will learn how Kotlin handles those tasks in the upcoming dispatcher chapters.

---

## The Relationship To Earlier Concepts

Recall:

```text
Main Thread
```

from Doc 1.

We learned:

```text
Blocking Main Thread
↓
UI Freeze
↓
ANR
```

The Main Dispatcher does not change that rule.

If you execute expensive work on:

```text
Dispatchers.Main
```

you can still freeze the UI.

The Main Dispatcher simply ensures:

```text
Work Executes On The Main Thread
```

It does not make expensive work safe.

---

## First Warning

The following is usually a bad idea:

```kotlin
launch(Dispatchers.Main) {

    performHeavyCalculation()
}
```

because:

```text
Heavy Work
↓
Main Thread Busy
↓
UI Cannot Respond
```

The application may become unresponsive.

---

## The Key Mental Model

```text
Dispatcher
↓
Chooses Thread
```

---

```text
Main Dispatcher
↓
Chooses Main Thread
```

Whenever a coroutine must interact with Android UI:

```text
Coroutine
↓
Dispatchers.Main
↓
Main Thread
↓
UI Work
```

---

## Revision Table

| Section | Content |
|----------|----------|
| Type | Dispatcher |
| Main Purpose | Execute coroutine work on the Main Thread |
| Used For | UI-related work |
| Safe For UI Updates? | Yes |
| Safe For Heavy Work? | No |
| Survives Suspension? | Yes |
| Previous Concept | Dispatcher |
| Next Concept | IO Dispatcher |

---

## Questions We Are Intentionally Postponing

You may naturally wonder:

```text
How Do We Run Network Requests?

How Do We Run Database Operations?

How Do We Run CPU-Intensive Work?

What Dispatcher Should Those Use?
```

These questions lead directly to:

```text
IO Dispatcher

Default Dispatcher
```

which we will explore next.

For this chapter, focus on one idea:

```text
Dispatchers.Main
```

means:

```text
Execute This Coroutine On The Main Thread
```
