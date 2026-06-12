# await

## What Problem Were We Facing?

In the previous chapter, we learned:

```text
async
↓
Create Coroutine
↓
Return Deferred
```

We also learned that:

```text
Deferred
```

represents a result that may become available in the future.

This immediately raises an important question:

```text
How Do We Get The Actual Result?
```

Suppose:

```text
async
↓
Downloads User Data
```

Eventually we want:

```text
User Object
```

not merely:

```text
Deferred
```

This is where:

```text
await
```

comes in.

---

## Abstraction Level

In this chapter we are discussing await from a developer's perspective.

We are NOT yet discussing:

```text
Continuation Internals

Thread Scheduling

Dispatcher Implementation
```

Those topics will come later.

For now, we are focused on:

```text
What await Does

Why It Exists

How It Relates To Deferred
```

---

## First Intuition

Imagine ordering food at a restaurant.

You receive:

```text
Order Token
```

The token is useful.

However:

```text
You Don't Want The Token
```

You want:

```text
The Food
```

Eventually you return and collect your order.

Conceptually:

```text
Order Token
↓
Wait Until Food Ready
↓
Receive Food
```

await plays a similar role.

---

## Definition

await is used to obtain the result represented by a Deferred.

Conceptually:

```text
Deferred
↓
await
↓
Actual Result
```

---

## Basic Example

Suppose:

```kotlin
val userDeferred = async {
    fetchUser()
}
```

At this point:

```text
User Result May Not Be Ready Yet
```

To obtain the result:

```kotlin
val user = userDeferred.await()
```

Conceptually:

```text
Deferred
↓
await
↓
User Object
```

---

## Mental Model

Think of:

```text
Deferred
```

as:

```text
Future Result
```

and:

```text
await
```

as:

```text
Give Me The Result When It Becomes Available
```

---

## What Happens If The Result Is Already Ready?

Suppose:

```text
Coroutine Finished
↓
Result Available
```

When await is called:

```text
Result Returned Immediately
```

No waiting is necessary.

Conceptually:

```text
Deferred
↓
Result Already Ready
↓
await
↓
Immediate Result
```

---

## What Happens If The Result Is Not Ready Yet?

Suppose:

```text
Coroutine Still Running
```

The requested result does not exist yet.

In that case:

```text
await
```

cannot return the result immediately.

Instead:

```text
Coroutine Suspends
```

until the result becomes available.

Conceptually:

```text
await
↓
Result Not Ready
↓
Suspend
↓
Result Arrives
↓
Resume
↓
Return Result
```

Notice how many earlier concepts come together here:

```text
Suspension

Resume

Continuation
```

---

## Another Mental Model

Imagine tracking a delivery.

You have:

```text
Tracking Number
```

but not yet:

```text
Package
```

Eventually the package arrives.

Conceptually:

```text
Tracking Number
↓
Wait
↓
Receive Package
```

Similarly:

```text
Deferred
↓
await
↓
Receive Result
```

---

## await Does NOT Mean Blocking

This is extremely important.

A common misconception is:

```text
await
↓
Blocks Thread
```

Incorrect.

If the result is not ready:

```text
Coroutine
↓
Suspends
```

Earlier we learned:

```text
Suspension ≠ Blocking
```

This distinction remains critical.

---

## Why Is await Needed?

A natural question is:

```text
Why Doesn't async Simply Return The Result?
```

Because:

```text
Result May Not Exist Yet
```

The work may still be running.

Therefore:

```text
async
↓
Returns Deferred

await
↓
Obtains Result Later
```

These two concepts work together.

---

## async And await Together

Notice how these concepts form a pair.

```text
async
↓
Create Future Result

await
↓
Obtain Future Result
```

Conceptually:

```text
async
↓
Work Starts

Deferred
↓
Represents Future Result

await
↓
Obtain Actual Result
```

---

## Important Clarification

A common misconception:

```text
await
↓
Starts Work
```

Incorrect.

The work was already started by:

```text
async
```

await only retrieves the result.

---

## Another Important Clarification

A common misconception:

```text
await Always Suspends
```

Incorrect.

If the result is already available:

```text
await
↓
Returns Immediately
```

Suspension happens only when necessary.

---

## Why Is await Important?

Without await:

```text
Deferred
```

would remain:

```text
Future Result
```

forever.

await provides the bridge between:

```text
Future Result
```

and:

```text
Actual Result
```

---

## Looking Back

Notice how our mental model is evolving:

```text
Coroutine Builder
↓
Creates Coroutine

launch
↓
Return Job

async
↓
Return Deferred

await
↓
Obtain Result
```

Many earlier concepts now connect together.

---

## The Next Question

At this point a natural question appears:

```text
Okay...

Where Can I Call launch?

Where Can I Call async?

Who Owns These Coroutines?
```

These questions lead us to:

```text
CoroutineScope
```

which provides the environment in which coroutines are created and managed.

---

## Common Misconceptions

### Misconception 1

```text
await Starts Work
```

Incorrect.

async starts the work.

---

### Misconception 2

```text
await Always Suspends
```

Incorrect.

Suspension occurs only if necessary.

---

### Misconception 3

```text
await Blocks Thread
```

Incorrect.

Suspension is not blocking.

---

### Misconception 4

```text
Deferred = Actual Result
```

Incorrect.

Deferred represents a future result.

---

## Relationship With Future Concepts

await directly connects to:

```text
Deferred

CoroutineScope

Job

Structured Concurrency
```

These concepts explain how coroutines are organized and managed.

---

## Revision Table

| Section | Content |
|----------|----------|
| Definition | Obtain result from Deferred |
| Works With | Deferred |
| Starts Work? | No |
| May Suspend? | Yes |
| Always Suspends? | No |
| Blocks Thread? | No |
| Abstraction Level | Result Retrieval |
| Previous Concept | async |
| Next Concept | CoroutineScope |
| Related Concepts | Deferred, Job |

---

## Future Exploration

- CoroutineScope
- Job
- Structured Concurrency
- Dispatchers
