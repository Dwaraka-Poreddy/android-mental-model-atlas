# Flow

## A Question From The Previous Chapter

In the previous chapter we learned:

```text
Suspend Functions
```

are excellent for:

```text
One Value
```

problems.

---

However, many real-world situations produce:

```text
Many Values Over Time
```

Examples:

```text
Location Updates

Chat Messages

Database Changes

Stock Prices

UI State Changes
```

These situations motivated the creation of:

```text
Flow
```

A natural question appears:

```text
What Exactly Is A Flow?
```

This chapter answers that question.

---

## First Mental Model

Think of:

```text
Flow
```

as:

```text
A Stream Of Values
```

Conceptually:

```text
Value 1
↓
Value 2
↓
Value 3
↓
Value 4
```

Instead of producing:

```text
One Result
```

Flow can produce:

```text
Many Results
```

over time.

---

## Revisiting suspend Functions

Recall:

```kotlin
suspend fun loadUser(): User
```

Conceptually:

```text
Start Work
↓
Produce User
↓
Finished
```

The function completes after producing:

```text
One Value
```

---

Now imagine:

```text
Temperature Updates
```

Conceptually:

```text
25°C
↓
26°C
↓
24°C
↓
27°C
```

There is no natural:

```text
Finished
```

after the first value.

New values keep arriving.

---

This is the kind of problem Flow was designed to represent.

---

## What Does A Flow Represent?

Conceptually:

```text
Flow
```

represents:

```text
A Sequence Of Values
```

that become available over time.

---

Think:

```text
Not:

One Value
```

---

Think:

```text
Many Values
```

---

Conceptually:

```text
Time
↓
Value
↓
Value
↓
Value
↓
Value
```

---

The values may arrive:

```text
Immediately

Later

Continuously

Forever
```

Flow does not care.

Its purpose is simply to represent:

```text
A Stream Of Data
```

---

## A Simple Example

Imagine a weather application.

Conceptually:

```text
25°C
↓
26°C
↓
24°C
↓
27°C
```

The weather keeps changing.

---

Flow allows us to model:

```text
Temperature Updates
```

as:

```text
One Stream
```

instead of repeatedly asking for:

```text
One Temperature
```

at a time.

---

## Another Mental Model

Earlier we used:

```text
Photograph
```

for:

```text
Suspend Function
```

because it captures:

```text
One Moment
```

---

Now think of:

```text
Flow
```

as:

```text
A Live Video Feed
```

A video is not:

```text
One Frame
```

It is:

```text
Frame
↓
Frame
↓
Frame
↓
Frame
```

over time.

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
Live Stream
```

This mental model is extremely useful.

---

## Flow Does Not Mean Infinite

A common misconception is:

```text
Flow = Infinite Data
```

Incorrect.

A Flow can produce:

```text
One Value

Three Values

Ten Values

One Million Values
```

or:

```text
Never Ending Values
```

---

The important idea is not:

```text
How Many Values
```

The important idea is:

```text
Potentially More Than One
```

---

## Flow Does Not Mean Real-Time

Another common misconception is:

```text
Flow = Real-Time Updates
```

Incorrect.

---

A Flow can produce:

```text
All Values Immediately
```

---

Or:

```text
Values Slowly Over Time
```

---

Or:

```text
Values Whenever They Become Available
```

---

Flow only describes:

```text
How Data Is Represented
```

not:

```text
How Fast Data Arrives
```

---

## Relationship To Coroutines

Earlier we learned:

```text
Coroutines
```

solve:

```text
Execution Problems
```

Examples:

```text
Suspension

Resumption

Cancellation

Failure
```

---

Flow builds on top of that foundation.

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

This is why Flow belongs naturally in the coroutine ecosystem.

---

## The Key Mental Model

Flow is Kotlin's way of representing:

```text
A Stream Of Values
```

over time.

---

Conceptually:

```text
Suspend Function
↓
One Value
```

---

```text
Flow
↓
Many Values
Over Time
```

That is the most important idea in this chapter.

---

## Revision Table

| Section | Content |
|----------|----------|
| Type | Core Concept |
| Main Purpose | Represent streams of data |
| suspend Function Represents | One Value |
| Flow Represents | Many Values |
| Flow Mental Model | Stream |
| Flow Mental Model | Live Video Feed |
| Flow Solves | Data Movement |
| Previous Concept | Why Flow Exists |
| Next Concept | Flow vs Suspend Function |

---

## A Natural Question

We now understand:

```text
What A Flow Is
```

A natural question appears:

```text
If Both
Suspend Functions
And
Flow
Can Produce Data,

When Should We Use One Versus The Other?
```

How do we decide whether something should be represented as:

```text
One Value
```

or:

```text
A Stream Of Values?
```

This question leads us to:

```text
Flow vs Suspend Function
```

which we will explore next.

---

For this chapter, focus on one idea:

```text
Flow
```

represents:

```text
A Stream Of Values
```

that may produce:

```text
One
Many
or Continuous Values Over Time
```
