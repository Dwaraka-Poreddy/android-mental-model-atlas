# Resume

## What Problem Were We Facing?

In the previous chapter, we learned about:

```text
Suspension
```

We learned that a coroutine can:

```text
Start
↓
Do Some Work
↓
Suspend
```

This immediately raises an important question:

```text
If A Coroutine Can Pause,

How Does It Continue Later?
```

A coroutine that suspends is expected to continue at some point.

That continuation is known as:

```text
Resume
```

---

## Abstraction Level

In this chapter we are discussing coroutine behavior from a developer's perspective.

We are NOT yet discussing:

```text
Continuation

Compiler Transformations

State Machines

Coroutine Internals
```

Those topics will come later.

For now, we are focused on:

```text
What Resume Means

Why It Happens

What Causes It
```

---

## First Intuition

Imagine reading a book.

You stop reading at:

```text
Page 125
```

and place a bookmark.

Later you return and continue from:

```text
Page 126
```

You did not restart the book.

You continued from where you left off.

This is analogous to:

```text
Resume
```

---

## Definition

Resume is the act of continuing a previously suspended coroutine.

Conceptually:

```text
Start
↓
Do Some Work
↓
Suspend
↓
Resume
↓
Continue Working
```

The important idea is:

```text
Resume ≠ Restart
```

The coroutine continues from where it was paused.

---

## What Resume Does NOT Mean

A common misconception:

```text
Resume = Start Again
```

Incorrect.

Consider:

```text
Step 1
↓
Step 2
↓
Suspend
↓
Resume
```

After resuming:

```text
Step 3
```

runs next.

Not:

```text
Step 1
```

again.

The coroutine continues from its previous position.

---

## Why Does A Coroutine Resume?

A coroutine usually suspends because it is waiting for something.

Examples:

```text
Network Response

Database Result

Timer

User Input
```

Eventually that required result becomes available.

At that point:

```text
Coroutine
↓
Resumes
```

and continues executing.

---

## Real World Example

Suppose:

```text
Download User Data
```

The flow might look like:

```text
Send Request
↓
Suspend
↓
Response Arrives
↓
Resume
↓
Process Response
```

Notice what happened:

```text
Coroutine
↓
Paused

Coroutine
↓
Continued Later
```

The coroutine did not restart from the beginning.

---

## Another Mental Model

Imagine ordering food at a restaurant.

```text
Place Order
↓
Wait For Food
```

While waiting:

```text
You Talk To Friends

Check Your Phone

Do Other Things
```

Eventually:

```text
Food Arrives
```

At that moment:

```text
You Return To Eating
```

Conceptually:

```text
Order Food
↓
Pause Activity
↓
Food Arrives
↓
Resume Activity
```

The important idea is:

```text
Resume Happens When The Required Result Becomes Available
```

---

## Suspension And Resume Together

Suspension and Resume are two parts of the same story.

```text
Suspend
↓
Pause

Resume
↓
Continue
```

A coroutine typically moves through:

```text
Start
↓
Work
↓
Suspend
↓
Resume
↓
Work
↓
Finish
```

---

## Important Clarification

Resume does NOT mean:

```text
Coroutine Chooses To Continue By Itself
```

Something causes the coroutine to resume.

Examples:

```text
Network Response Arrives

Database Result Available

Timer Completes
```

At this level, it is enough to understand:

```text
Required Result Available
↓
Coroutine Resumes
```

The implementation details will come later.

---

## Another Important Clarification

Resume does NOT mean:

```text
Immediately Continue On The Same Thread
```

That discussion belongs to future chapters.

Later we will study:

```text
Dispatchers

Thread Switching
```

For now, think only in terms of:

```text
Coroutine Suspends
↓
Coroutine Resumes
```

---

## Why Is This Important?

Suspension alone is not useful.

A coroutine must eventually continue.

The combination of:

```text
Suspend

Resume
```

allows coroutines to express asynchronous operations in a clean and readable way.

Without resume:

```text
Suspension
```

would simply be:

```text
Stopping
```

instead of:

```text
Pausing
```

---

## Looking Back

Notice how our mental model has evolved.

```text
Coroutine
↓
Unit Of Suspendable Work

Suspension
↓
Temporary Pause

Resume
↓
Continue Later
```

We are slowly building the complete coroutine story.

---

## The Next Question

At this point a natural question appears:

```text
Can A Coroutine Suspend Anywhere?
```

For example:

```text
Can It Suspend Here?

Can It Suspend There?

Who Decides?
```

These questions lead us to:

```text
Suspension Point
```

which we will study next.

---

## Common Misconceptions

### Misconception 1

```text
Resume = Restart
```

Incorrect.

A coroutine continues from where it paused.

---

### Misconception 2

```text
Resume Happens Automatically For No Reason
```

Incorrect.

Something causes the coroutine to resume.

---

### Misconception 3

```text
Resume Means Same Thread
```

Not necessarily.

Thread discussions come later.

---

### Misconception 4

```text
Resume Creates A New Coroutine
```

Incorrect.

The existing coroutine continues.

---

## Relationship With Future Concepts

Resume directly connects to:

```text
Suspension Point

Suspend Function

Continuation
```

These concepts explain where suspension can happen and how Kotlin remembers where to continue.

---

## Revision Table

| Section | Content |
|----------|----------|
| Definition | Continue a previously suspended coroutine |
| Resume Means | Continue |
| Resume Does Not Mean | Restart |
| Typical Trigger | Required result becomes available |
| Works Together With | Suspension |
| Abstraction Level | Coroutine Behavior |
| Previous Concept | Suspension |
| Next Concept | Suspension Point |
| Related Concepts | Suspend Function, Continuation |

---

## Future Exploration

- Suspension Point
- Suspend Function
- Continuation
- Dispatchers
