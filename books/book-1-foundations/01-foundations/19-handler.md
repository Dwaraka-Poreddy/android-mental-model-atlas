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

More importantly:

```text
Why would anyone need
to put work into a queue?
```

To answer that question, we need to understand a very common Android problem.

---

## The Real Problem

Suppose a user taps:

```text
Login Button
```

The application starts a network request.

```text
Main Thread
↓
Start Network Request

Background Thread
↓
Download Data
```

A few seconds later:

```text
Background Thread
↓
Data Download Complete
```

Now a new problem appears:

```text
The Data Is Ready

But The UI Must Be Updated
```

And we already learned:

```text
UI Updates
↓
Must Happen On
Main Thread
```

This creates a challenge:

```text
Background Thread
↓
Needs Main Thread
To Do Something
```

How can one thread request work from another thread?

This is one of the problems Handler helps solve.

---

## Abstraction Level

In this chapter we are discussing Android's message-processing and thread-communication model.

To keep the discussion focused, we will use phrases such as:

```text
Thread Requests Work

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

A Handler is an Android component that can submit work into a thread's Message Queue.

Conceptually:

```text
Handler
↓
Submit Work

Message Queue
↓
Store Work
```

This allows one thread to request work from another thread.

---

## Mental Model

Imagine a restaurant.

The kitchen is already busy.

Suppose an employee receives a special request:

```text
Customer Wants Extra Cheese
```

The employee does not run into the kitchen and start cooking.

Instead:

```text
Write Request
↓
Place Request In Kitchen Queue
```

The kitchen will eventually process it.

A Handler plays a similar role.

It submits requests into a queue so that the target thread can process them later.

---

## The Core Idea

A useful mental model is:

```text
Thread A
↓
Needs Something

Handler
↓
Submit Request

Message Queue
↓
Store Request

Looper
↓
Retrieve Request

Thread B
↓
Process Request
```

This is the key intuition behind Handler.

---

## Where Does The Work Come From?

A common misconception is:

```text
Handler Creates Work
```

Incorrect.

The work usually originates from:

```text
User Input

Network Responses

Timers

Database Results

System Events

Other Threads
```

The Handler does not invent work.

It simply transports work into a Message Queue.

Conceptually:

```text
Event Occurs
↓
Work Needed

Handler
↓
Submit Work

Message Queue
↓
Store Work
```

---

## Android Example

Suppose:

```text
User Clicks Login
```

The application starts downloading data.

```text
Background Thread
↓
Download User Data
```

Eventually:

```text
Data Ready
```

Now the UI must update.

```text
Background Thread
↓
Needs Main Thread
```

The Handler can be used to submit a request into the Main Thread's queue.

Conceptually:

```text
Background Thread
↓
Data Ready

Handler
↓
Submit UI Update

Main Thread Queue
↓
Store UI Update

Main Thread Looper
↓
Retrieve UI Update

Main Thread
↓
Update Screen
```

This is one of the most important historical uses of Handlers.

---

## Why Not Just Call The Function Directly?

This is an important question.

Suppose we're already on:

```text
Main Thread
```

and want to execute:

```text
updateUI()
```

We can simply do:

```text
updateUI()
```

No Handler required.

---

Handlers become useful when:

```text
Current Thread
≠
Target Thread
```

Example:

```text
Background Thread
↓
Needs Main Thread
```

The Handler helps bridge that gap.

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
Submit Work

Looper
↓
Retrieve Work

Thread
↓
Process Work
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

## Posting Work For Later

A Handler can also schedule work to happen in the future.

Conceptually:

```text
Submit Work
↓
Wait 2 Seconds
↓
Process Work
```

This can be useful for:

```text
Timers

Retries

Delayed UI Updates

Scheduled Actions
```

---

## Why Were Handlers Important?

Before coroutines became common, Handlers were one of Android's primary tools for:

```text
Thread Communication

Message Passing

UI Updates

Delayed Work
```

Many Android APIs were built around this model.

Understanding Handlers helps explain older Android codebases.

---

## Another Useful Mental Model

Think of the flow as:

```text
Something Happens
↓
Work Needed

Handler
↓
Submit Request

Message Queue
↓
Hold Request

Looper
↓
Retrieve Request

Thread
↓
Process Request

CPU
↓
Execute Instructions
```

---

## Why Is This Important?

We now understand the complete message-processing pipeline:

```text
Handler
↓
Submit Work

Message Queue
↓
Store Work

Looper
↓
Retrieve Work

Thread
↓
Process Work
```

This architecture powered Android applications for many years.

However, another question naturally appears:

```text
This seems like a lot of moving parts.

What happens when
asynchronous work
becomes much more complex?
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
Handler Creates Work
```

Incorrect.

It only submits work.

---

### Misconception 2

```text
Handler Executes Work
```

Incorrect.

The target thread processes the work.

---

### Misconception 3

```text
Handler Stores Work
```

Incorrect.

The Message Queue stores work.

---

### Misconception 4

```text
Handler Is The Looper
```

Incorrect.

They have different responsibilities.

---

### Misconception 5

```text
Handler Is Mainly About Posting Work
```

Partially true.

A more useful mental model is:

```text
Handler
↓
Bridge To A Thread's Queue
```

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
| Definition | Submits work into a thread's Message Queue |
| Creates Work? | No |
| Executes Work? | No |
| Stores Work? | No |
| Main Purpose | Request work on a target thread |
| Mental Model | Bridge To A Thread's Queue |
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
