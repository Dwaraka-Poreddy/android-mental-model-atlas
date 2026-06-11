# Looper

## What Problem Were We Facing?

In the previous chapter, we learned about Message Queues.

A Message Queue stores work that needs to be processed.

Example:

```text
Message Queue

├── Handle Button Click
├── Draw Animation Frame
└── Process Network Result
```

However, this raises an important question:

```text
The queue stores work.

But how does that work
ever get processed?
```

A queue can hold:

```text
10 Messages

100 Messages

1000 Messages
```

but:

```text
Stored Work
≠
Processed Work
```

Something must continuously retrieve work from the queue and make sure it gets processed.

That component is:

```text
Looper
```

---

## Abstraction Level

In this chapter we are discussing Android's threading and event-processing model.

To keep the discussion focused, we will use phrases such as:

```text
Thread Processes Message

Thread Handles Work
```

At a lower level:

```text
CPU
↓
Executes Instructions

For

Thread
```

which we learned earlier in the Foundations section.

However, from an Android architecture perspective, it is more useful to think in terms of:

```text
Message Queue
↓
Looper
↓
Thread
```

than:

```text
Message Queue
↓
Looper
↓
CPU
```

Both views are correct.

This chapter focuses on the threading view.

---

## Definition

A Looper is a component that continuously retrieves messages from a Message Queue and dispatches them to a thread for processing.

Conceptually:

```text
Message Queue
↓
Looper Retrieves Message
↓
Thread Processes Message
```

The Looper coordinates work.

The thread performs work.

---

## Mental Model

Imagine a restaurant kitchen.

Orders arrive throughout the day.

```text
Burger

Pizza

Pasta
```

The orders are placed into:

```text
Order Queue
```

Someone must continuously:

```text
Take Next Order

Give It To Chef

Take Next Order

Give It To Chef
```

That role is analogous to:

```text
Looper
```

The chef is analogous to:

```text
Thread
```

The chef prepares the food.

The Looper simply keeps supplying work.

---

## The Core Idea

A useful mental model is:

```text
Message Queue
↓
Stores Work

Looper
↓
Selects Next Work

Thread
↓
Processes Work

CPU
↓
Executes Instructions
```

Each component has a different responsibility.

---

## What Does A Looper Actually Do?

Conceptually:

```text
Loop Forever

Get Next Message

Dispatch Message

Repeat
```

Visualized:

```text
Message Queue
↓
Message A
↓
Looper Retrieves Message
↓
Thread Processes Message A

Message Queue
↓
Message B
↓
Looper Retrieves Message
↓
Thread Processes Message B
```

This cycle continues for as long as the thread remains active.

---

## Why Is It Called "Looper"?

Because it continuously performs a loop.

Conceptually:

```text
while (true)
{
    Get Next Message
    Dispatch Message
}
```

Do not focus on the code.

Focus on the behavior:

```text
Check Queue
↓
Get Work
↓
Dispatch Work
↓
Repeat
```

forever.

---

## What Looper Does NOT Do

Understanding what a Looper does NOT do is just as important as understanding what it does.

### Looper Does Not Execute Instructions

The CPU executes instructions.

```text
CPU
↓
Executes Instructions
```

The Looper never directly executes code.

Its responsibility is:

```text
Retrieve Message
↓
Dispatch Message
```

---

### Looper Does Not Create Work

The Looper does not generate new work.

It only handles work that already exists.

```text
Message Queue
↓
Stores Work

Looper
↓
Retrieves Work
```

---

### Looper Does Not Store Work

The Looper is not the queue.

```text
Message Queue
↓
Stores Messages

Looper
↓
Retrieves Messages
```

These are separate responsibilities.

---

## Android Main Thread Example

The Android Main Thread has:

```text
Message Queue
+
Looper
```

This allows it to continuously process:

```text
Touch Events

UI Updates

Animations

System Events
```

Conceptually:

```text
User Tap
↓
Message Queue
↓
Looper Retrieves Message
↓
Main Thread Processes Message
```

---

## Why The Main Thread Doesn't "Finish"

Many beginners imagine:

```text
Main Thread Starts
↓
Processes Work
↓
Finishes
```

But that isn't what happens.

The Main Thread is expected to remain alive for the lifetime of the application.

Conceptually:

```text
Get Message
↓
Process Message

Get Message
↓
Process Message

Get Message
↓
Process Message
```

continuously.

This behavior is enabled by the Looper.

---

## Is Looper Only For The Main Thread?

No.

The concept of an event loop exists in many systems.

Examples:

```text
Operating Systems

Servers

JavaScript Runtime

Game Engines
```

Android's Main Thread is simply the example most Android developers interact with.

---

## Does Every Thread Have A Looper?

No.

This is an extremely important distinction.

A thread can exist without:

```text
Looper

Message Queue
```

Example:

```text
Start Thread
↓
Do Work
↓
Finish
```

This thread does not need either.

A Looper is useful when a thread must continuously process queued work.

---

## Android Example

Suppose Android receives:

```text
Button Click
```

The work enters the Message Queue.

```text
Button Click
↓
Message Queue
```

The Looper later retrieves it.

```text
Message Queue
↓
Looper Retrieves Message
```

The Main Thread then processes it.

```text
Main Thread
↓
Handle Click
```

Without the Looper:

```text
Messages Accumulate
```

but nothing continuously retrieves them.

---

## Looper And UI Freeze

Suppose the Main Thread becomes blocked.

Example:

```text
Network Request
↓
Wait 5 Seconds
```

During those 5 seconds:

```text
Looper
↓
Cannot Continue Retrieving Messages
```

Meanwhile:

```text
Message Queue
↓
Keeps Growing
```

This is one reason UI freezes occur.

---

## Another Useful Mental Model

Think of the relationship like this:

```text
Message Queue
↓
What Should Be Done?

Looper
↓
What Should Be Done Next?

Thread
↓
Handles The Work

CPU
↓
Executes Instructions
```

---

## Why Is This Important?

We now understand:

```text
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

However, another question remains:

```text
How does work get into
the Message Queue
in the first place?
```

Something must be able to:

```text
Create Work

Schedule Work

Post Work
```

into the queue.

That component is:

```text
Handler
```

which we will study next.

---

## Common Misconceptions

### Misconception 1

```text
Looper Executes Work
```

Incorrect.

The Looper dispatches work.

The CPU executes instructions for the thread.

---

### Misconception 2

```text
Looper Creates Work
```

Incorrect.

It only retrieves existing work.

---

### Misconception 3

```text
Looper Is The Queue
```

Incorrect.

They are separate concepts.

---

### Misconception 4

```text
Every Thread Has A Looper
```

Incorrect.

Many threads do not.

---

### Misconception 5

```text
Looper Is Android-Specific
```

Incorrect.

Event loops exist throughout software engineering.

---

## Relationship With Future Concepts

Looper directly connects to:

```text
Message Queue

Handler

Main Thread

UI Events

Dispatchers.Main

Coroutines
```

Understanding Looper makes Android's event-processing model much easier to understand.

---

## Revision Table

| Section | Content |
|----------|----------|
| Definition | Retrieves messages and dispatches them for processing |
| Executes Instructions? | No |
| Creates Work? | No |
| Stores Work? | No |
| Main Thread Uses One? | Yes |
| Every Thread Has One? | No |
| Mental Model | Dispatcher / Coordinator |
| Abstraction Level | Threading Model |
| Previous Concept | Message Queue |
| Next Concept | Handler |
| Related Concepts | Main Thread, Event Loop, Handler |

---

## Future Exploration

- Handler
- Event Loops
- Dispatchers.Main
- Android Runtime Internals
