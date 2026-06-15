# Flow vs Suspend Function

## A Question From The Previous Chapter

In the previous chapter we learned:

```text
Flow
```

represents:

```text
A Stream Of Values
```

over time.

A natural question appears:

```text
When Should I Use A Suspend Function &
When Should I Use Flow?
```

This chapter answers that question.

---

## Why Do Both Exist?

Earlier we learned:

```kotlin
suspend fun loadUser(): User
```

and:

```kotlin
fun observeUser(): Flow<User>
```

Both can eventually provide:

```text
User Data
```

A natural question appears:

```text
Why Doesn't Kotlin Just Use One?
```

Because these APIs solve:

```text
Different Problems
```

The most important question is:

```text
How Many Results Do You Need?
```

---

## Example 1: Load User Profile

Imagine:

```kotlin
suspend fun loadUser(): User
```

Question:

```text
How Many Results Will This Produce?
```

Answer:

```text
One
```

Conceptually:

```text
Request User
↓
Receive User
↓
Finished
```

This is naturally a:

```text
Suspend Function
```

because the problem only requires:

```text
One Answer
```

---

## Example 2: Observe User Profile

Now imagine:

```kotlin
fun observeUser(): Flow<User>
```

Question:

```text
Can The User Change?
```

Answer:

```text
Yes
```

Examples:

```text
Name Updated
Profile Picture Updated
Settings Changed
```

Conceptually:

```text
User Version A
↓
User Version B
↓
User Version C
```

This is naturally a:

```text
Flow
```

because the problem requires:

```text
Ongoing Updates
```

rather than:

```text
One Final Answer
```

---

## Example 3: Fetch Product

Imagine:

```kotlin
suspend fun fetchProduct(
    id: String
): Product
```

Question:

```text
Do We Need Continuous Product Updates?
```

Usually:

```text
No
```

We simply need:

```text
The Current Product
```

Conceptually:

```text
Request Product
↓
Receive Product
↓
Finished
```

A suspend function is a natural fit.

---

## Example 4: Observe Shopping Cart

Imagine:

```kotlin
fun observeCart(): Flow<Cart>
```

Question:

```text
Can The Cart Change?
```

Answer:

```text
Yes
```

Examples:

```text
Item Added
Item Removed
Quantity Changed
```

Conceptually:

```text
Cart Version A
↓
Cart Version B
↓
Cart Version C
```

A Flow is a natural fit.

---

## First Mental Model

Think of:

```text
Suspend Function
```

as:

```text
A Phone Call
```

You ask:

```text
What Is The Temperature?
```

---

You receive:

```text
25°C
```

---

The call ends.

Conceptually:

```text
Question
↓
Answer
↓
Finished
```

---

Think of:

```text
Flow
```

as:

```text
A Radio Broadcast
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

New updates continue arriving whenever they become available.

---

## A Quick Rule Of Thumb

A useful question to ask is:

```text
Do I Need
An Answer
Or
Updates?
```

---

If you need:

```text
An Answer
```

think:

```text
Suspend Function
```

---

If you need:

```text
Updates
```

think:

```text
Flow
```

---

Conceptually:

```text
One Answer
↓
Suspend Function
```

---

```text
Ongoing Updates
↓
Flow
```

---

## Flow Does Not Replace Suspend Functions

A common misconception is:

```text
Flow Is The Modern Version Of Suspend Functions
```

Incorrect.

Suspend functions remain the most natural representation for many problems.

Examples:

```text
Load User
Fetch Product
Download File
Calculate Value
```

These problems naturally produce:

```text
One Result
```

---

## Suspend Functions Do Not Replace Flow

Another common misconception is:

```text
Suspend Functions Make Flow Unnecessary
```

Incorrect.

Many real-world problems naturally produce:

```text
Changing Data
```

Examples:

```text
Location Updates
Database Changes
Chat Messages
UI State Changes
```

These problems naturally produce:

```text
Many Results Over Time
```

---

## The Key Mental Model

The difference between:

```text
Suspend Function
```

and:

```text
Flow
```

is not:

```text
Old
vs
New
```

The difference is:

```text
One Answer
vs
Ongoing Updates
```

---

Conceptually:

```text
Suspend Function
↓
Request
↓
Response
↓
Finished
```

---

```text
Flow
↓
Receive Update
↓
Receive Update
↓
Receive Update
```

Understanding this distinction makes it much easier to choose the correct abstraction.

---

## Revision Table

| Section | Content |
|----------|----------|
| Type | Comparison & Decision Making |
| Main Question | Which One Should I Use? |
| Suspend Function Best For | One Answer |
| Flow Best For | Ongoing Updates |
| Suspend Function Mental Model | Phone Call |
| Flow Mental Model | Radio Broadcast |
| Decision Rule | Answer vs Updates |
| Previous Concept | Flow |
| Next Concept | Cold Flow |

---

## A Natural Question

We now understand:

```text
When To Use Flow
```

instead of:

```text
Suspend Functions
```

A natural question appears:

```text
How Does A Flow Actually Behave?
```

Suppose we define:

```kotlin
val flow = ...
```

Does it immediately start producing values?

Or does something need to happen first?

This question leads us to:

```text
Cold Flow
```

which we will explore next.

---

For this chapter, focus on one idea:

```text
Suspend Function
```

is usually the natural choice when you need:

```text
One Answer
```

---

```text
Flow
```

is usually the natural choice when you need:

```text
Ongoing Updates
```
