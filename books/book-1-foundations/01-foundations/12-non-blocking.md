# Non-Blocking

## What Problem Were We Facing?

In the previous chapter, we learned about blocking.

Blocking occurs when a thread must wait before it can continue.

Example:

```text
Send Network Request
↓
Wait 5 Seconds
↓
Receive Response
```

During those 5 seconds:

```text
Thread Cannot Continue
```

The thread is blocked.

This raises an important question:

```text
Do we really need to keep
an entire thread waiting?
```

Could we allow the thread to do something else while waiting?

The answer leads us to:

```text
Non-Blocking
```

---

## Definition

Non-blocking execution is an approach where a thread does not sit idle waiting for an operation to complete.

Instead:

```text
Start Operation
↓
Continue Doing Other Work
↓
Come Back Later When Result Is Ready
```

The thread remains available to perform useful work.

---

## Mental Model

Imagine a restaurant worker.

A customer asks:

```text
Can I get a pizza?
```

The pizza takes:

```text
20 Minutes
```

to prepare.

---

### Blocking Approach

The worker does:

```text
Take Order
↓
Stand Near Oven
↓
Wait 20 Minutes
↓
Deliver Pizza
```

During those 20 minutes:

```text
No Other Customers Helped
```

---

### Non-Blocking Approach

The worker does:

```text
Take Order
↓
Send Pizza To Kitchen
↓
Help Other Customers
↓
Pizza Ready
↓
Deliver Pizza
```

Now the worker remains productive.

---

## Blocking vs Non-Blocking

### Blocking

```text
Start Work
↓
Wait
↓
Continue
```

Thread remains occupied.

---

### Non-Blocking

```text
Start Work
↓
Leave Work In Progress
↓
Do Something Else
↓
Come Back Later
```

Thread remains available.

---

## Example

Suppose a network request takes:

```text
5 Seconds
```

---

### Blocking Version

```text
Thread
↓
Send Request
↓
Wait 5 Seconds
↓
Receive Response
```

For the entire duration:

```text
Thread Is Busy Waiting
```

---

### Non-Blocking Version

```text
Thread
↓
Send Request
↓
Continue Other Work
↓
Response Arrives
↓
Handle Response
```

The thread remains useful.

---

## Why Is Non-Blocking Valuable?

Consider:

```text
1000 Network Requests
```

If each request blocks a thread:

```text
1000 Threads Waiting
```

This consumes:

- Memory
- Scheduling Resources
- Context Switching Overhead

even though most threads are doing nothing.

---

With a non-blocking approach:

```text
Few Threads
↓
Many Operations In Progress
```

Resource usage becomes much more efficient.

---

## Android Example

Suppose Instagram needs:

```text
Download Feed

Download Stories

Download Notifications
```

---

Blocking approach:

```text
Thread 1 Waiting

Thread 2 Waiting

Thread 3 Waiting
```

Many threads spend most of their time idle.

---

Non-blocking approach:

```text
Start Downloads
↓
Thread Free
↓
Responses Arrive
↓
Handle Results
```

Fewer threads can manage more work.

---

## Important Clarification

A common misunderstanding is:

```text
Non-Blocking
=
Work Happens Instantly
```

Incorrect.

The operation still takes time.

Example:

```text
Network Request
↓
Still Takes 5 Seconds
```

The difference is:

```text
Thread Is Not Forced To Wait
```

---

## Another Important Clarification

Non-blocking does NOT mean:

```text
No Waiting Exists
```

Waiting still exists.

The difference is:

```text
Operation Waits
```

instead of:

```text
Thread Waits
```

This distinction is extremely important.

---

## Visual Comparison

Blocking:

```text
Thread
↓
Wait...
↓
Wait...
↓
Wait...
↓
Continue
```

---

Non-Blocking:

```text
Thread
↓
Start Operation
↓
Other Work
↓
Other Work
↓
Other Work
↓
Result Ready
↓
Continue
```

---

## Why Is This Important For Coroutines?

This chapter is one of the biggest stepping stones toward coroutines.

Traditional blocking model:

```text
Wait
↓
Block Thread
```

Coroutine model:

```text
Wait
↓
Suspend Work
↓
Free Thread
```

This is one of the core reasons coroutines became popular.

Coroutines make non-blocking programming significantly easier.

---

## Common Misconceptions

### Misconception 1

```text
Non-Blocking = Faster Network
```

Incorrect.

The network speed is unchanged.

---

### Misconception 2

```text
Non-Blocking = No Waiting
```

Incorrect.

Waiting still exists.

---

### Misconception 3

```text
Non-Blocking = Parallelism
```

Incorrect.

These are different concepts.

---

### Misconception 4

```text
Blocking Is Always Wrong
```

Incorrect.

Blocking is sometimes perfectly acceptable.

The decision depends on the situation.

---

## Relationship With Future Concepts

Non-blocking directly connects to:

```text
Concurrency

Parallelism

Coroutines

Suspension

Dispatchers

Async Programming
```

Many modern Android APIs are built around non-blocking principles.

---

## Revision Table

| Section | Content |
|----------|----------|
| Definition | Thread remains free while operation is in progress |
| Core Idea | Do not keep thread waiting |
| Waiting Removed? | No |
| Thread Waiting Removed? | Often Yes |
| Main Benefit | Better resource utilization |
| Previous Concept | Blocking |
| Next Concept | Concurrency |
| Related Concepts | Coroutines, Suspension, Async Programming |

---

## Future Exploration

- Async Programming
- Futures
- Promises
- Reactive Streams
- Coroutines
- Suspension
