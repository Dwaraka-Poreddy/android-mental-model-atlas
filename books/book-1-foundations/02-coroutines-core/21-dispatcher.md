# Dispatcher

## A Question We Have Been Postponing

Throughout the previous chapters, several questions naturally appeared:

```text
What Happens To The Thread When A Coroutine Suspends?

Who Resumes A Coroutine?

Which Thread Runs A Coroutine?

Can A Coroutine Move Between Threads?
```

We intentionally postponed these questions because we were missing an important concept:

```text
Dispatcher
```

This chapter answers those questions.

---

## Coroutines Do Not Execute Themselves

So far we have learned:

```text
Coroutine
↓
Suspend
↓
Resume
```

However, a coroutine cannot execute itself.

Consider:

```kotlin
launch {

    loadUser()
}
```

A natural question appears:

```text
What Actually Executes This Coroutine?
```

The answer is:

```text
A Thread
```

Remember:

```text
Coroutines Are Not Threads
```

A coroutine is simply:

```text
A Unit Of Work
```

Eventually, some thread must execute that work.

---

## A New Problem Appears

If threads execute coroutines:

```text
Who Chooses The Thread?
```

For example:

```kotlin
launch {

    loadUser()
}
```

Should this run on:

```text
Main Thread?
```

or:

```text
Background Thread?
```

or:

```text
Some Other Thread?
```

Something must make that decision.

This is the problem that:

```text
Dispatcher
```

solves.

---

## Enter Dispatcher

Conceptually:

```text
Dispatcher
```

decides:

```text
Which Thread Should Execute A Coroutine
```

Think of:

```text
Coroutine
```

as:

```text
Work
```

and:

```text
Dispatcher
```

as:

```text
The Thread Assignment Rule
```

---

## First Mental Model

Imagine:

```text
Coroutine
```

is a task.

For example:

```text
Load User

Load Feed

Save Data
```

The dispatcher acts like:

```text
A Manager
```

that decides:

```text
Which Worker Should Perform The Task
```

Conceptually:

```text
Coroutine
↓
Dispatcher
↓
Thread
```

This is the most important relationship in this chapter.

---

## Coroutines And Threads

Before Dispatchers, many developers imagine:

```text
Coroutine
↓
Runs
```

However, the more accurate model is:

```text
Coroutine
↓
Dispatcher
↓
Thread
↓
Execution
```

A coroutine never executes directly.

A thread always executes it.

The dispatcher decides which thread should be used.

---

## Revisiting Suspension

Consider:

```kotlin
launch {

    delay(1000)

    println("Done")
}
```

Earlier we learned:

```text
delay()
↓
Coroutine Suspends
↓
Continuation Saved
```

A natural question remained:

```text
What Happens To The Thread?
```

Now we can finally answer it.

---

## What Suspension Actually Means

When a coroutine suspends:

```text
Coroutine Stops Progressing
```

It does NOT mean:

```text
Thread Stops Existing
```

The coroutine says:

```text
I Cannot Continue Right Now
```

The dispatcher is then free to use available threads for other work.

Conceptually:

```text
Coroutine Suspends
↓
Thread No Longer Needed
↓
Other Work Can Run
```

This is one of the major reasons coroutines are efficient.

---

## Important Clarification

A common misconception is:

```text
Coroutine Suspends
↓
Thread Suspends
```

Incorrect.

Suspension is a property of:

```text
Coroutine
```

not:

```text
Thread
```

The coroutine pauses.

The thread can continue doing other work.

---

## Who Resumes The Coroutine?

Another postponed question was:

```text
Who Resumes The Coroutine?
```

Conceptually:

```text
Something Completes
↓
Coroutine Ready To Continue
↓
Dispatcher Schedules Execution
↓
Thread Continues Coroutine
```

The dispatcher participates in deciding where resumed coroutine execution happens.

---

## Can A Coroutine Change Threads?

A natural question appears:

```text
If A Coroutine Suspends...

Must It Resume On The Same Thread?
```

The answer is:

```text
Not Necessarily
```

A coroutine may:

```text
Start On One Thread
```

and later:

```text
Resume On Another Thread
```

depending on the dispatcher being used.

---

## Why Is This Possible?

Remember:

```text
Continuation
```

stores:

```text
Execution State
```

not:

```text
Thread State
```

The continuation remembers:

```text
Where To Continue
```

The dispatcher decides:

```text
Where To Run
```

These are separate concerns.

---

## Another Mental Model

Think of:

```text
Continuation
```

as:

```text
The Saved Progress
```

and:

```text
Dispatcher
```

as:

```text
The Future Thread Assignment
```

Conceptually:

```text
Continuation
↓
What To Execute Next

Dispatcher
↓
Where To Execute It
```

---

## What A Dispatcher Does NOT Do

A common misconception is:

```text
Dispatcher Executes The Coroutine
```

Not exactly.

Remember:

```text
Threads Execute Coroutines
```

The dispatcher decides:

```text
Which Thread Gets The Work
```

---

## Another Common Misconception

A common misconception is:

```text
Coroutine = Thread
```

Incorrect.

A coroutine is:

```text
Work
```

A thread is:

```text
The Resource That Executes The Work
```

The dispatcher connects the two.

---

## Why Dispatchers Exist

Without dispatchers, Kotlin would not know:

```text
Which Thread Should Execute A Coroutine
```

Different work may require:

```text
Different Threads
```

For example:

```text
UI Work

Network Work

CPU Work
```

Different dispatchers exist to support these different execution needs.

We will explore those dispatchers in the next chapters.

---

## The Key Mental Model

```text
Coroutines Do Not Execute Themselves
```

Instead:

```text
Coroutine
↓
Dispatcher
↓
Thread
↓
Execution
```

A dispatcher's primary responsibility is deciding:

```text
Which Thread Executes The Coroutine
```

---

## Revision Table

| Section | Content |
|----------|----------|
| Type | Coroutine Context Element |
| Main Purpose | Decide which thread executes a coroutine |
| Executes Coroutines? | No |
| Chooses Threads? | Yes |
| Related To Suspension? | Yes |
| Related To Resumption? | Yes |
| Previous Concept | supervisorScope |
| Next Concepts | Main Dispatcher, IO Dispatcher, Default Dispatcher, Unconfined Dispatcher |

---

## Questions We Are Intentionally Postponing

You may naturally wonder:

```text
What Is Main Dispatcher?

What Is IO Dispatcher?

What Is Default Dispatcher?

What Is Unconfined Dispatcher?

How Does Kotlin Actually Choose Threads?
```

These are exactly the questions that the upcoming chapters will answer.

For this chapter, focus on one idea:

```text
Coroutine
↓
Dispatcher
↓
Thread
```

The dispatcher decides which thread executes a coroutine.
