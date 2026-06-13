# Unconfined Dispatcher

## A Strange Dispatcher

So far we have learned:

```text
Main Dispatcher
↓
UI Work
```

---

```text
IO Dispatcher
↓
Waiting Work
```

---

```text
Default Dispatcher
↓
CPU Work
```
--- 

A pattern has emerged:

```text
Dispatcher
↓
Chooses Thread
```

Every dispatcher we have seen so far provides a clear rule about:

```text
Which Thread Should Execute The Coroutine
```

For example:

```text
Main Dispatcher
↓
Main Thread
```

---

```text
IO Dispatcher
↓
IO Threads
```

---

```text
Default Dispatcher
↓
CPU Threads
```

A natural question appears:

```text
What Happens If A Dispatcher Does Not Enforce A Specific Thread?
```

This question leads us to:

```text
Dispatchers.Unconfined
```

---

## A New Idea

Every dispatcher we have seen so far enforces a thread selection rule.

For example:

```text
Main Dispatcher
↓
Use Main Thread
```

---

```text
IO Dispatcher
↓
Use IO Threads
```

---

```text
Default Dispatcher
↓
Use CPU Threads
```

Each dispatcher has a strong opinion about:

```text
Where Execution Happens
```

Unconfined is different.

---

## Enter Unconfined Dispatcher

Conceptually:

```text
Dispatchers.Unconfined
```

means:

```text
Do Not Strongly Enforce A Specific Thread
```

This is the defining behavior of Unconfined.

---

## First Mental Model

Think of:

```text
Main Dispatcher
```

as:

```text
Always Use The Main Thread
```

---

Think of:

```text
IO Dispatcher
```

as:

```text
Use Threads Intended For Waiting Work
```

---

Think of:

```text
Default Dispatcher
```

as:

```text
Use Threads Intended For CPU Work
```

---

Think of:

```text
Unconfined Dispatcher
```

as:

```text
No Strong Thread Rule
```

---

## Why Is This Strange?

By now we have learned:

```text
Coroutine
↓
Dispatcher
↓
Thread
```

The dispatcher normally answers:

```text
Which Thread Should Execute The Coroutine?
```

With Unconfined:

```text
The Dispatcher Does Not Strongly Care
```

This is very different from the other dispatchers.

---

## Example

```kotlin
launch(Dispatchers.Unconfined) {

    println("Hello")
}
```

A natural question appears:

```text
Which Thread Will Execute This?
```

The answer is:

```text
It Depends
```

This uncertainty is exactly why Unconfined is considered an advanced dispatcher.

---

## Revisiting Suspension

Consider:

```kotlin
launch(Dispatchers.Unconfined) {

    delay(1000)

    println("Done")
}
```

A natural question appears:

```text
Will It Resume On The Same Thread?
```

The answer is:

```text
Not Necessarily
```

Unlike:

```text
Main Dispatcher

IO Dispatcher

Default Dispatcher
```

Unconfined does not strongly enforce where resumed execution should happen.

---

## Why Does This Matter?

Consider:

```kotlin
launch(Dispatchers.Main) {

    delay(1000)

    updateUI()
}
```

We know:

```text
Resumes On Main Thread
```

because:

```text
Main Dispatcher
```

continues enforcing its thread rule.

---

Now consider:

```kotlin
launch(Dispatchers.Unconfined) {

    delay(1000)

    continueWork()
}
```

The resumed execution may occur on a different thread.

This behavior is one of the reasons Unconfined can be surprising.

---

## Another Mental Model

Think of:

```text
Main

IO

Default
```

as:

```text
Strongly Opinionated Dispatchers
```

They have clear thread preferences.

---

Think of:

```text
Unconfined
```

as:

```text
Weakly Opinionated Dispatcher
```

It does not strongly constrain thread selection.

---

## A Natural Question

At this point, a natural question appears:

```text
Why Would Anyone Want This?
```

This is an excellent question.

Most application code does not need Unconfined.

In fact:

```text
Most Android Code Should Not Use It
```

---

## Why Does Unconfined Exist?

There are situations where developers want:

```text
Minimal Thread Constraints
```

Examples include:

```text
Framework Internals

Testing

Advanced Coroutine Behavior
```

In these situations:

```text
Strict Thread Selection
```

may be unnecessary or undesirable.

---

## Important Clarification

A common misconception is:

```text
Unconfined = Random Thread
```

Incorrect.

The behavior is more subtle than that.

The important idea is:

```text
No Strong Thread Rule
```

not:

```text
Random Thread Rule
```

---

## Another Common Misconception

A common misconception is:

```text
Unconfined Is Faster
```

Incorrect.

Unconfined exists because of its execution semantics.

Not because of performance.

---

## Another Common Misconception

A common misconception is:

```text
Unconfined Is A Better Default
```

Incorrect.

For most application code:

```text
Main

IO

Default
```

are usually much easier to reason about.

---

## Another Common Misconception

A common misconception is:

```text
Unconfined Avoids Dispatchers
```

Incorrect.

Unconfined is itself a dispatcher.

It simply follows different execution rules.

---

## The Relationship To Earlier Concepts

Recall:

```text
Continuation
```

from earlier chapters.

We learned:

```text
Continuation
↓
Remembers What Runs Next
```

and:

```text
Dispatcher
↓
Decides Where It Runs
```

Unconfined changes:

```text
How Strongly Thread Selection Is Enforced
```

but these core concepts remain the same.

---

## Comparing The Dispatchers

```text
Main Dispatcher
↓
UI Work
```

---

```text
IO Dispatcher
↓
Waiting Work
```

---

```text
Default Dispatcher
↓
CPU Work
```

---

```text
Unconfined Dispatcher
↓
No Strong Thread Rule
```

This comparison captures the essence of all four dispatchers.

---

## The Key Mental Model

Most dispatchers answer:

```text
Which Thread Should Execute This Coroutine?
```

with a clear answer.

---

Unconfined answers:

```text
Do Not Strongly Enforce A Particular Thread
```

This single idea explains why Unconfined behaves differently from the other dispatchers.

---

## Revision Table

| Section | Content |
|----------|----------|
| Type | Dispatcher |
| Main Purpose | Execute without strongly enforcing a particular thread |
| Intended For | Advanced use cases |
| Intended For UI Work? | Usually No |
| Intended For Waiting Work? | Usually No |
| Intended For CPU Work? | Usually No |
| Easy To Reason About? | Less Than Other Dispatchers |
| Previous Concept | Default Dispatcher |
| Next Concept | Dispatcher Internals |

---

## Questions We Are Intentionally Postponing

You may naturally wonder:

```text
What Thread Does Unconfined Actually Start On?

What Thread Does It Resume On?

How Does Kotlin Implement This Behavior?

What Really Happens After Suspension?
```

These are excellent questions.

Answering them properly requires concepts we have not yet learned:

```text
Dispatcher Internals

Coroutine Scheduling
```

We will explore those topics in future chapters.

For this chapter, focus on one idea:

```text
Dispatchers.Unconfined
```

means:

```text
Do Not Strongly Enforce A Particular Thread
```
