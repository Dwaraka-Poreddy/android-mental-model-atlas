# Why suspend Exists

## What Problem Were We Facing?

In the previous chapter, we learned:

```text
suspend Function
↓
Function Allowed To Suspend A Coroutine
```

This immediately raises an important question:

```text
Why Did Kotlin Need The suspend Keyword?
```

Why not simply allow:

```text
Every Function
```

to suspend?

Why introduce a special keyword at all?

Understanding the answer helps explain one of the most important design decisions in Kotlin Coroutines.

---

## Abstraction Level

In this chapter we are discussing the purpose of the suspend keyword from a design perspective.

We are NOT yet discussing:

```text
Continuation Internals

Compiler Transformations

Generated State Machines
```

Those topics will come later.

For now, we are focused on:

```text
Why Kotlin Needed An Explicit Marker For Suspension
```

---

## First Intuition

Imagine reading a piece of code:

```kotlin
loadUser()
updateUI()
saveAnalytics()
```

Without any additional information, you might assume:

```text
Function Call
↓
Runs
↓
Returns
```

This is how normal functions behave.

Now imagine any function could suddenly suspend.

A reader would no longer know:

```text
Can This Pause?

Can This Continue Later?

Can This Change Timing?
```

just by looking at the code.

---

## Kotlin's Design Goal

Kotlin wanted suspension to be:

```text
Explicit
```

rather than:

```text
Hidden
```

The language designers wanted developers to immediately see:

```text
Suspension May Occur Here
```

instead of discovering it later through documentation or debugging.

---

## Mental Model

Think of:

```text
suspend
```

as a warning sign.

Conceptually:

```text
Normal Function
↓
No Suspension Expected

suspend Function
↓
Suspension Possible
```

The keyword communicates important information to both:

```text
Developer

Compiler
```

---

## Why Not Allow Every Function To Suspend?

A natural question is:

```text
Why Not Make Every Function Suspendable?
```

At first this sounds convenient.

However, it would make suspension:

```text
Implicit
```

rather than:

```text
Explicit
```

Developers reading code would have a harder time identifying locations where execution may pause and continue later.

The suspend keyword makes those locations visible.

---

## Real World Example

Suppose you see:

```kotlin
fetchUser()
```

Without additional information, you do not know:

```text
Normal Function?

Suspend Function?
```

Now compare:

```kotlin
suspend fun fetchUser()
```

Immediately you know:

```text
Suspension May Occur
```

This makes the code easier to understand and reason about.

---

## Another Mental Model

Imagine driving on a road.

Certain areas contain:

```text
School Zone
```

signs.

The sign does not force a child to appear.

The sign communicates:

```text
Special Conditions May Exist Here
```

Similarly:

```text
suspend
```

communicates:

```text
Special Execution Behavior May Exist Here
```

---

## Benefits Of The suspend Keyword

### Better Readability

Developers can quickly identify:

```text
Possible Suspension Locations
```

in a codebase.

---

### Better Predictability

Suspension becomes:

```text
Explicit
```

instead of:

```text
Surprising
```

---

### Better Compiler Support

The compiler can clearly distinguish:

```text
Normal Functions

Suspend Functions
```

and apply different rules when necessary.

We will learn more about this in future chapters.

---

## Important Clarification

The suspend keyword does NOT mean:

```text
Suspension Will Happen
```

It only means:

```text
Suspension May Happen
```

This distinction remains important.

---

## Another Important Clarification

The suspend keyword does NOT exist because:

```text
Background Thread Needed
```

Thread selection is handled separately.

The purpose of:

```text
suspend
```

is to communicate and control suspension behavior.

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

Why suspend Exists
↓
Make Suspension Explicit
```

---

## Why Is This Important?

Without the suspend keyword:

```text
Suspension Behavior Would Be Harder To See
```

The keyword makes coroutine behavior easier to understand and reason about.

It turns:

```text
Hidden Behavior
```

into:

```text
Visible Behavior
```

---

## The Next Question

At this point a natural question appears:

```text
Okay...

A suspend Function Can Suspend.

But How Does Kotlin Know Where To Continue Later?
```

This question leads us to:

```text
Continuation
```

which is one of the most important concepts in the entire coroutine system.

---

## Common Misconceptions

### Misconception 1

```text
suspend Exists Because Of Threads
```

Incorrect.

Threads and suspension are different concerns.

---

### Misconception 2

```text
suspend Means Suspension Will Happen
```

Incorrect.

Suspension is allowed, not guaranteed.

---

### Misconception 3

```text
Every Function Should Automatically Suspend
```

Not Kotlin's design choice.

Kotlin prefers explicit suspension behavior.

---

### Misconception 4

```text
suspend Exists Only For Performance
```

Incorrect.

Readability and predictability are also major reasons.

---

## Relationship With Future Concepts

Why suspend Exists directly connects to:

```text
Continuation

Coroutine Builders

Dispatchers
```

These concepts explain how Kotlin actually implements pause-and-resume behavior.

---

## Revision Table

| Section | Content |
|----------|----------|
| Main Question | Why does suspend exist? |
| Main Answer | Make suspension explicit |
| Purpose | Visibility and predictability |
| Always Suspends? | No |
| Related To Threads? | No |
| Abstraction Level | Design Perspective |
| Previous Concept | suspend Function |
| Next Concept | Continuation |
| Related Concepts | Dispatchers, Coroutine Builders |

---

## Future Exploration

- Continuation
- Coroutine Builders
- Dispatchers
