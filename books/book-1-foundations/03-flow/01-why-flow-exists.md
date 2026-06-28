# Why Flow Exists

## A Question From The Previous Document

In the previous document we learned:

```text
Coroutines
```

solve the problem of:

```text
Asynchronous Work
```

We learned how coroutines:

```text
Start

Suspend

Resume

Cancel

Fail
```

A natural question appears:

```text
How Does Data Move Through An Application?
```

This question begins our exploration of:

```text
Flow
```

Before learning:

```text
What Flow Is
```

we must first understand:

```text
Why Flow Exists
```

---

## Revisiting suspend Functions

Earlier we learned:

```kotlin
suspend fun loadUser(): User
```

Conceptually:

```text
Start Work
↓
Wait
↓
Receive Result
```

This is an excellent model when we need:

```text
One Result
```

Examples:

```text
Load User

Load Product

Read File

Calculate Value
```

Conceptually:

```text
Request
↓
Response
```

---

## First Mental Model

Think of a suspend function as:

```text
A Question
```

asked to the system.

For example:

```text
What Is The User Profile?
```

The system eventually responds:

```text
Here Is The User Profile
```

---

Conceptually:

```text
Question
↓
Answer
```

Once the answer arrives:

```text
The Interaction Ends
```

---

## A Natural Question

Suppose we have:

```kotlin
suspend fun getTemperature(): Int
```

Conceptually:

```text
Request Temperature
↓
Receive Temperature
```

---

Imagine:

```text
10:00 AM
↓
25°C
```

The function returns:

```text
25°C
```

and finishes.

---

A natural question appears:

```text
What Happens At 10:01 AM?
```

Suppose the temperature becomes:

```text
26°C
```

How does our function tell us?

---

The answer is:

```text
It Doesn't
```

The function already finished.

---

## The Limitation

A suspend function can return:

```text
One Result
```

After that:

```text
Its Job Is Done
```

Conceptually:

```text
Request
↓
Response
↓
Finished
```

This works perfectly for:

```text
Single Result Problems
```

However:

```text
Not All Data Behaves This Way
```

---

## The Real World Problem

Many kinds of data:

```text
Change Over Time
```

Examples:

```text
Location Updates

Stock Prices

Chat Messages

Database Changes

Download Progress

UI State Changes
```

These are not:

```text
One Value
```

problems.

They are:

```text
Many Values Over Time
```

problems.

---

## Another Mental Model

Imagine:

```text
A Water Tap
```

A suspend function is like:

```text
Opening The Tap Once
↓
Filling One Glass
↓
Done
```

---

Many real-world problems look more like:

```text
Water Continuously Flowing
```

Conceptually:

```text
New Value
↓
New Value
↓
New Value
↓
New Value
```

The data keeps arriving.

---

## A Failed Solution

One possible approach is:

```text
Keep Calling The suspend Function Again And Again
```

Conceptually:

```text
Request
↓
Response

Request
↓
Response

Request
↓
Response
```

This is often called:

```text
Polling
```

---

A natural question appears:

```text
Should We Really Keep Asking Forever?
```

In many situations:

```text
No
```

We would rather be notified when:

```text
New Data Exists
```

instead of repeatedly asking for it.

---

## The Missing Concept

At this point we have identified a new problem.

We need something that can represent:

```text
Multiple Values
```

not just:

```text
One Value
```

---

Conceptually:

```text
Suspend Function
↓
One Value
```

---

What we need is:

```text
Something That Can Produce

Value 1
↓
Value 2
↓
Value 3
↓
Value 4
```

over time.

---

## Why Coroutines Alone Are Not Enough

Coroutines solve:

```text
How Work Executes
```

Examples:

```text
Where It Runs

When It Suspends

When It Resumes

When It Cancels
```

---

However, coroutines do not automatically solve:

```text
How Data Flows
```

through a system.

This is a different problem.

---

Conceptually:

```text
Coroutines
↓
Execution
```

---

```text
Flow
↓
Data Movement
```

Both are important.

Both solve different problems.

---

## The Birth Of Flow

The Kotlin team needed a way to represent:

```text
A Stream Of Values
```

instead of:

```text
A Single Value
```

Conceptually:

```text
One Value
↓
Suspend Function
```

---

```text
Many Values Over Time
↓
Flow
```

This is the fundamental reason Flow exists.

---

## Another Mental Model

Think of:

```text
suspend fun
```

as:

```text
A Photograph
```

You capture:

```text
One Moment
```

in time.

---

Think of:

```text
Flow
```

as:

```text
A Live Video Stream
```

You continue receiving:

```text
New Frames
```

as time passes.

---

Conceptually:

```text
Suspend Function
↓
Snapshot
```

---

```text
Flow
↓
Continuous Stream
```

---

## The Key Mental Model

Flow was not created because:

```text
Coroutines Were Missing Features
```

Flow was created because:

```text
A Different Problem Needed Solving
```

---

Conceptually:

```text
Coroutines
↓
How Work Runs
```

---

```text
Flow
↓
How Data Moves
```

---

```text
Suspend Function
↓
One Value
```

---

```text
Flow
↓
Many Values Over Time
```

Understanding this distinction is the foundation for everything that follows in the Flow document.

---

## Revision Table

| Section | Content |
|----------|----------|
| Type | Motivation Chapter |
| Main Problem | Represent changing data |
| suspend Function Returns | One Value |
| Real World Data Often Produces | Many Values |
| Coroutines Solve | Execution |
| Flow Solves | Data Movement |
| suspend Function Mental Model | Photograph |
| Flow Mental Model | Live Video Stream |
| Previous Document | Coroutines Core |
| Next Concept | Flow |

---

## A Natural Question

We now understand:

```text
Why Flow Exists
```

A natural question appears:

```text
What Exactly Is A Flow?
```

How does Flow represent:

```text
Multiple Values Over Time?
```

How are those values produced?

How are they received?

This question leads us to:

```text
Flow
```

which we will explore next.

---

For this chapter, focus on one idea:

```text
Suspend Functions
```

are excellent for:

```text
One Result
```

---

However:

```text
Many Real World Problems Produce Many Results Over Time
```

---

That problem led to the creation of:

```text
Flow
```
