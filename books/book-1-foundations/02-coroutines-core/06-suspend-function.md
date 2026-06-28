# suspend Function

## What Problem Were We Facing?

In the previous chapter, we learned:

```text
Coroutine
↓
Can Suspend

But Only At Suspension Points
```

This immediately raises an important question:

```text
Who Creates These Suspension Points?
```

How does Kotlin know:

```text
Suspension Allowed Here

Suspension Not Allowed Here
```

The answer involves:

```text
suspend Function
```

---

## Abstraction Level

In this chapter we are discussing coroutine programming from a developer's perspective.

We are NOT yet discussing:

```text
Continuation

Compiler Transformations

Generated State Machines
```

Those topics will come later.

For now, we are focused on:

```text
What A suspend Function Is

Why It Exists

How It Relates To Suspension
```

---

## First Intuition

Consider a normal function:

```kotlin
fun loadUser() {
    ...
}
```

A normal function:

```text
Starts
↓
Runs
↓
Returns
```

It has no built-in ability to suspend a coroutine.

Now consider:

```kotlin
suspend fun loadUser() {
    ...
}
```

The keyword:

```text
suspend
```

tells Kotlin:

```text
This Function May Suspend A Coroutine
```

---

## Definition

A suspend Function is a function that is allowed to suspend a coroutine.

Conceptually:

```text
Normal Function
↓
Cannot Suspend Coroutine

suspend Function
↓
May Suspend Coroutine
```

Notice the wording:

```text
May Suspend
```

not:

```text
Will Suspend
```

---

## Why Does Kotlin Need This?

Recall what we learned earlier:

```text
Coroutine
↓
Cannot Suspend Anywhere
```

Suspension is allowed only at:

```text
Suspension Points
```

Kotlin needs a way to explicitly mark places where suspension may occur.

That mechanism is:

```text
suspend Function
```

---

## Mental Model

Think of a suspend Function as:

```text
A Function That Has Permission To Pause A Coroutine
```

Conceptually:

```text
Normal Function
↓
No Suspension Permission

suspend Function
↓
Suspension Permission
```

---

## Suspension Point Connection

Earlier we learned:

```text
Suspension Point
↓
Valid Location To Suspend
```

One of the most common ways Suspension Points are introduced is through:

```text
suspend Functions
```

Conceptually:

```text
Call suspend Function
↓
Possible Suspension Point
```

This is why suspend Functions are such a central part of coroutine programming.

---

## suspend Does NOT Mean Suspension Will Happen

This is extremely important.

A common misconception:

```text
suspend Function
↓
Always Suspends
```

Incorrect.

A suspend Function simply means:

```text
Suspension Is Allowed
```

not:

```text
Suspension Is Guaranteed
```

---

## Example

Suppose:

```kotlin
suspend fun getCachedUser() {
    ...
}
```

If the data is already available:

```text
Immediate Result
↓
No Suspension Needed
```

The function may complete without any suspension at all.

---

## Another Mental Model

Imagine entering a building.

Some rooms are marked:

```text
Authorized Personnel Only
```

The sign means:

```text
Permission Exists
```

It does not mean:

```text
You Must Enter
```

Similarly:

```text
suspend
```

means:

```text
Suspension Allowed
```

not:

```text
Suspension Required
```

---

## What suspend Does NOT Mean

### It Does Not Mean Background Thread

A common misconception:

```text
suspend Function
↓
Runs On Background Thread
```

Incorrect.

The keyword:

```text
suspend
```

says nothing about threads.

Thread selection is a separate topic.

---

### It Does Not Mean Async By Itself

Another misconception:

```text
suspend Function
↓
Automatically Starts Async Work
```

Incorrect.

The keyword simply allows suspension.

It does not automatically create concurrency.

---

### It Does Not Mean Always Slow

Another misconception:

```text
suspend Function
↓
Must Be Long Running
```

Incorrect.

A suspend Function may return almost immediately.

---

## Why Is This Important?

The suspend keyword is one of the foundations of Kotlin Coroutines.

Without it:

```text
Kotlin Would Not Know Where Suspension May Occur
```

It acts as an explicit marker in the codebase.

---

## Looking Back

Notice how our mental model is evolving:

```text
Coroutine
↓
Suspendable Work

Suspension
↓
Temporary Pause

Resume
↓
Continue Later

Suspension Point
↓
Valid Place To Suspend

suspend Function
↓
Function Allowed To Suspend
```

Each concept builds naturally on the previous one.

---

## The Next Question

At this point a natural question appears:

```text
Okay...

Why Did Kotlin Need The suspend Keyword At All?
```

Why not simply allow every function to suspend?

That question leads us to:

```text
Why suspend Exists
```

which we will study next.

---

## Common Misconceptions

### Misconception 1

```text
suspend Function Always Suspends
```

Incorrect.

Suspension is allowed, not guaranteed.

---

### Misconception 2

```text
suspend Function Runs On Background Thread
```

Incorrect.

Threads are a separate concern.

---

### Misconception 3

```text
suspend Function Automatically Creates Concurrency
```

Incorrect.

The keyword only allows suspension.

---

### Misconception 4

```text
Every suspend Function Must Be Slow
```

Incorrect.

A suspend Function may complete immediately.

---

## Relationship With Future Concepts

suspend Function directly connects to:

```text
Why suspend Exists

Continuation

Coroutine Builders

Dispatchers
```

These concepts explain why Kotlin chose this design and how suspension actually works.

---

## Revision Table

| Section | Content |
|----------|----------|
| Definition | Function allowed to suspend a coroutine |
| Always Suspends? | No |
| Introduces | Potential Suspension Points |
| Related To Threads? | No |
| Main Purpose | Explicitly mark possible suspension |
| Abstraction Level | Coroutine Programming |
| Previous Concept | Suspension Point |
| Next Concept | Why suspend Exists |
| Related Concepts | Continuation, Dispatchers |

---

## Future Exploration

- Why suspend Exists
- Continuation
- Coroutine Builders
- Dispatchers
