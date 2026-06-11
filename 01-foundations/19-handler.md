# Handler

## What Problem Were We Facing?

In the previous chapters, we learned:

```text
Message Queue
↓
Stores Work
```

and:

```text
Looper
↓
Retrieves Messages
↓
Dispatches Them For Processing
```

This leaves one important question unanswered:

```text
How does work get into
the Message Queue
in the first place?
```

Something must be able to:

```text
Create Work

Schedule Work

Submit Work
```

for future processing.

That component is:

```text
Handler
```

---

## Abstraction Level

In this chapter we are discussing Android's message-processing model.

To keep the discussion focused, we will use phrases such as:

```text
Handler Posts Work

Thread Processes Work
```

At a lower level:

```text
CPU
↓
Executes Instructions
```

However, from an Android architecture perspective, it is more useful to think in terms of:

```text
Handler
↓
Message Queue
↓
Looper
↓
Thread
```

This chapter focuses on that higher-level view.

---

## Definition

A Handler is an Android component used to post work into a Message Queue.

Conceptually:

```text
Handler
↓
Posts Message
↓
Message Queue
```

The Handler is one of the primary ways work enters the queue.

---

## Mental Model

Let's revisit our restaurant analogy.

We already learned:

```text
Order Queue
↓
Stores Orders

Kitchen Manager
↓
Assigns Orders

Chef
↓
Prepares Food
```

But now a question appears:

```text
Who puts new orders
into the queue?
```

That role is analogous to:

```text
Handler
```

The Handler submits new work into the system.

---

## The Core Idea

A useful mental model is:

```text
Handler
↓
Posts Work

Message Queue
↓
Stores Work

Looper
↓
Retrieves Work

Thread
↓
Processes Work

CPU
↓
Executes Instructions
```

Each component has a separate responsibility.

---

## What Does A Handler Actually Do?

Conceptually:

```text
Create Message
↓
Place Message In Queue
```

Visualized:

```text
Handler
↓
Post Message A

Message Queue

├── Message A
```

Later:

```text
Handler
↓
Post Message B
```

Queue becomes:

```text
Message Queue

├── Message A
├── Message B
```

The Handler's responsibility ends once the work has been placed into the queue.

---

## Handler Does Not Execute Work

A common misconception:

```text
Handler Executes Work
```

Incorrect.

The Handler only submits work.

```text
Handler
↓
Posts Work

Looper
↓
Retrieves Work

Thread
↓
Processes Work
```

---

## Handler Does Not Store Work

Another misconception:

```text
Handler
=
Message Queue
```

Incorrect.

The queue stores work.

The Handler submits work.

---

## Android Example

Suppose a background thread finishes downloading data.

It wants the UI to update.

Conceptually:

```text
Background Thread
↓
Data Ready

Handler
↓
Post UI Update

Message Queue
↓
Store UI Update

Looper
↓
Retrieve UI Update

Main Thread
↓
Update Screen
```

This is one of the classic uses of Handlers in Android.

---

## Posting Work For Later

A Handler can also schedule work to happen in the future.

Conceptually:

```text
Post Message
↓
Wait 2 Seconds
↓
Process Message
```

This is useful for:

```text
Timers

Delayed UI Updates

Retries

Scheduled Tasks
```

---

## Why Were Handlers Important?

Before coroutines became common, Handlers were one of the primary mechanisms used for:

```text
Thread Communication

UI Updates

Delayed Work

Message Passing
```

Many Android APIs were built around this model.

Understanding Handlers helps explain the design of many older Android systems.

---

## Handler And The Main Thread

The Main Thread's Message Queue is especially important.

A Handler can post work into:

```text
Main Thread Queue
```

which allows code running elsewhere to request:

```text
Update UI

Show Toast

Refresh Screen
```

at a later time.

---

## Is Handler Android-Specific?

Mostly yes.

The broader idea of:

```text
Submit Work To Queue
```

exists in many systems.

However:

```text
Handler
```

is a specific Android API that implements this pattern.

---

## Another Useful Mental Model

Think of the flow as:

```text
Handler
↓
"I Have Work"

Message Queue
↓
"I Am Holding Work"

Looper
↓
"What's Next?"

Thread
↓
"I'll Process It"

CPU
↓
"I'll Execute Instructions"
```

---

## Why Is This Important?

We now understand the complete message-processing pipeline:

```text
Handler
↓
Posts Work

Message Queue
↓
Stores Work

Looper
↓
Retrieves Work

Thread
↓
Processes Work
```

This model powered a huge amount of Android development for many years.

However, another question naturally appears:

```text
This seems like a lot of moving parts.

What happens when asynchronous
work becomes more complicated?
```

As applications grew larger, developers began encountering problems with:

```text
Too Many Threads

Complex Coordination

Nested Callbacks
```

These challenges eventually led to:

```text
Traditional Thread Problems
```

which we will study next.

---

## Common Misconceptions

### Misconception 1

```text
Handler Executes Work
```

Incorrect.

It only posts work.

---

### Misconception 2

```text
Handler Stores Work
```

Incorrect.

The queue stores work.

---

### Misconception 3

```text
Handler Is The Looper
```

Incorrect.

They have different responsibilities.

---

### Misconception 4

```text
Handler Is The Thread
```

Incorrect.

The Handler works with threads but is not a thread.

---

## Relationship With Future Concepts

Handler directly connects to:

```text
Message Queue

Looper

Main Thread

Thread Communication

Coroutines

Dispatchers.Main
```

Understanding Handlers makes many historical Android APIs easier to understand.

---

## Revision Table

| Section | Content |
|----------|----------|
| Definition | Posts work into a Message Queue |
| Executes Work? | No |
| Stores Work? | No |
| Main Responsibility | Submit Work |
| Mental Model | Order Submission Desk |
| Abstraction Level | Android Message Processing |
| Previous Concept | Looper |
| Next Concept | Traditional Thread Problems |
| Related Concepts | Message Queue, Looper, Main Thread |

---

## Future Exploration

- HandlerThread
- Dispatchers.Main
- Thread Communication
- Coroutines
- Android Runtime Internals
