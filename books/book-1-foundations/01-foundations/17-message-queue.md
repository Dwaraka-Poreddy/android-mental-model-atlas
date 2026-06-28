# Message Queue

## What Problem Were We Facing?

In previous chapters, we learned:

```text
Main Thread
↓
Responsible For UI Work
```

We also learned:

```text
User Taps Button
↓
Main Thread Handles Event

Update UI
↓
Main Thread Performs Work
```

This raises an important question:

```text
How does the Main Thread know
what work it needs to do?
```

How does it know:

```text
A Button Was Clicked

A Screen Needs To Be Redrawn

A Timer Finished

A Network Result Arrived
```

Where does all this work come from?

The answer begins with:

```text
Message Queue
```

---

## Definition

A Message Queue is a queue that stores work waiting to be processed.

Think of it as:

```text
A To-Do List
For Future Work
```

The work sits in the queue until something processes it.

---

## Mental Model

Imagine a customer service desk.

Customers arrive throughout the day.

Each customer submits a request:

```text
Reset Password

Update Address

Open New Account
```

The requests are placed into:

```text
Waiting Line
```

The employee later processes them one by one.

```text
Request A
↓
Request B
↓
Request C
```

The waiting line is analogous to:

```text
Message Queue
```

---

## Why Do We Need A Message Queue?

Remember:

```text
A Thread Can Only Execute
One Thing At A Time
```

Meanwhile, work can arrive from many places:

```text
Touch Events

UI Updates

Animations

Timers

Network Results

System Events
```

All of these cannot execute immediately.

A place is needed to temporarily store incoming work until it can be processed.

That place is:

```text
Message Queue
```

---

## Visual Example

Suppose:

```text
User Taps Button

Animation Frame Arrives

Network Result Arrives
```

Conceptually:

```text
Message Queue

├── Handle Button Click
├── Draw Animation Frame
└── Process Network Result
```

The work waits in the queue until it is processed.

---

## First-In First-Out (FIFO)

A queue generally follows:

```text
First In
First Out
```

Example:

```text
Message A Arrives

Message B Arrives

Message C Arrives
```

Processing order:

```text
A
↓
B
↓
C
```

This helps maintain predictable behavior.

---

## What Is A Message?

A message is simply:

```text
A Unit Of Work
```

Examples:

```text
Handle Click

Update Text

Run Animation

Execute Callback
```

The exact implementation details are not important yet.

The important idea is:

```text
Message
=
Something That Needs To Be Done
```

---

## Message Queue Does Not Execute Work

A very important distinction:

```text
Message Queue
≠
Worker
```

The queue only stores work.

Example:

```text
Message Queue

├── Task A
├── Task B
└── Task C
```

The queue itself executes nothing.

It simply holds work until something processes it.

---

## Is Message Queue Only For The Main Thread?

No.

Message Queues are a general software concept.

Many systems use:

```text
Queue
↓
Store Work

Worker
↓
Process Work
```

Examples include:

```text
Operating Systems

Servers

Event Loops

Distributed Systems
```

Android's Main Thread is simply one important example that we are studying.

---

## Android Example

Suppose a user taps:

```text
Login Button
```

Android may place something conceptually similar to:

```text
Handle Login Click
```

into the Main Thread's Message Queue.

Similarly:

```text
Update UI

Scroll Event

Animation Frame
```

may all enter the queue.

The queue stores the work until it can be processed.

---

## Why Message Queues Are Useful

Without a Message Queue:

```text
Events Arrive Randomly
```

There would be no organized way to coordinate incoming work.

The queue provides:

```text
Order

Coordination

Predictability
```

between:

```text
Work Producers
```

and:

```text
Work Consumers
```

---

## Message Queue And UI Freeze

Suppose the Main Thread becomes blocked.

Example:

```text
Network Request
↓
Wait 5 Seconds
```

During those 5 seconds:

```text
Message Queue

├── Touch Event
├── Scroll Event
├── Draw Event
└── Animation Event
```

Messages continue arriving.

However:

```text
Main Thread
↓
Busy
```

Nothing is processing the queue.

The queue keeps growing.

This is one reason UI freezes occur.

---

## Important Clarification

A common misconception:

```text
Main Thread
=
Message Queue
```

Incorrect.

The Message Queue stores work.

The Main Thread processes work.

They are different concepts.

---

## Another Important Clarification

A queue can contain:

```text
100 Messages
```

while:

```text
Main Thread
```

can still only process:

```text
One Message At A Time
```

The queue does not make execution parallel.

It simply organizes work.

---

## One More Important Clarification

Another common misconception is:

```text
Every Thread Automatically
Has A Message Queue
```

Not necessarily.

A thread can exist without processing messages from a queue.

We will learn in the next chapter that Android uses a component called:

```text
Looper
```

to connect a thread to a Message Queue and continuously process messages.

For now, simply remember:

```text
Message Queue
↓
Stores Work

Threads
↓
Execute Work
```

The connection between the two will become clear shortly.

---

## Why Is This Important?

We now understand:

```text
Message Queue
↓
Stores Work Waiting To Be Processed
```

However, an important question remains:

```text
How does a thread become connected
to a Message Queue?

How does work move from:

Message Queue
↓
Actual Execution?
```

The queue itself cannot execute work.

A thread executes work.

Something must continuously connect:

```text
Message Queue
↓
Thread
```

and keep processing incoming messages.

That component is:

```text
Looper
```

which we will study next.

---

## Common Misconceptions

### Misconception 1

```text
Message Queue Executes Work
```

Incorrect.

It only stores work.

---

### Misconception 2

```text
Main Thread And Message Queue
Are The Same Thing
```

Incorrect.

They work together but are different concepts.

---

### Misconception 3

```text
Every Thread Has A Message Queue
```

Incorrect.

Not automatically.

---

### Misconception 4

```text
Queue Means Parallel Execution
```

Incorrect.

The queue only organizes work.

---

## Relationship With Future Concepts

Message Queue directly connects to:

```text
Looper

Handler

Main Thread

UI Events

Coroutines

Dispatchers.Main
```

Understanding Message Queue makes Android's event-processing model much easier to understand.

---

## Revision Table

| Section | Content |
|----------|----------|
| Definition | Stores work waiting to be processed |
| Main Purpose | Organize incoming work |
| Executes Work? | No |
| Main Thread Uses One? | Yes |
| Every Thread Has One? | Not Automatically |
| Mental Model | To-Do List / Waiting Line |
| Previous Concept | ANR |
| Next Concept | Looper |
| Related Concepts | Handler, Main Thread, Event Loop |

---

## Future Exploration

- Looper
- Handler
- Event Loops
- Dispatchers.Main
- Android Runtime Internals
