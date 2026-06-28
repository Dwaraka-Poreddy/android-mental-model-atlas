# Combining Flows

## A Question From The Previous Chapter

In the previous chapter we learned:

```text
debounce()
```

helps when:

```text
The Values Are Fine

But Reacting To Them Too Frequently Creates Problems
```

So far, every example we have seen has involved:

```text
One Flow
```

A natural question appears:

```text
What If The Data I Need Lives In Multiple Flows?
```

For example:

```text
User Information
```

may come from one Flow.

---

```text
User Settings
```

may come from another Flow.

---

Yet the UI needs:

```text
One Screen Model
```

containing both pieces of information.

How can multiple Flows work together?

This chapter answers that question.

---

## The Real Challenge

At first glance the problem seems simple:

```text
Flow A

+

Flow B
```

---

However, a deeper question appears:

```text
Flows Emit Independently
```

Nothing guarantees:

```text
Flow A
```

and:

```text
Flow B
```

emit at the same time.

---

One Flow may emit:

```text
Every Second
```

---

Another may emit:

```text
Every Minute
```

---

Another may emit:

```text
Only Once
```

A natural question appears:

```text
How Can We Build One Result

If The Inputs Arrive At Different Times?
```

This is the real problem that:

```text
combine()

zip()
```

solve.

---

## A Real Android Example

Imagine:

```kotlin
val userFlow: Flow<User>

val settingsFlow: Flow<UserSettings>
```

---

The UI needs:

```kotlin
data class UserScreenUiModel(
    val user: User,
    val settings: UserSettings
)
```

---

Conceptually:

```text
User Data
```

comes from:

```text
userFlow
```

---

```text
Settings Data
```

comes from:

```text
settingsFlow
```

---

The screen needs:

```text
Both
```

at the same time.

---

We need a way to:

```text
Combine Multiple Flows
```

into:

```text
One Flow
```

---

## The First Strategy: combine()

Think of:

```text
combine()
```

as:

```text
Always Use The Latest Value From Every Flow
```

---

Conceptually:

```text
Latest A
+
Latest B
↓
Emit Result
```

---

This is the most commonly used strategy in Android applications.

---

## First combine() Example

Consider:

```kotlin
val screenStateFlow =
    combine(
        userFlow,
        settingsFlow
    ) { user, settings ->

        UserScreenUiModel(
            user = user,
            settings = settings
        )
    }
```

Conceptually:

```text
User
+
Settings
↓
Screen Model
```

---

A new Flow is created that emits:

```text
UserScreenUiModel
```

instead of emitting:

```text
User
```

or:

```text
Settings
```

individually.

---

## What Does combine() Produce?

A natural question appears:

```text
After Combining Two Flows What Do We Get?
```

Consider:

```kotlin
val screenStateFlow =
    combine(
        userFlow,
        settingsFlow
    ) { user, settings ->

        UserScreenUiModel(
            user = user,
            settings = settings
        )
    }
```

The result is:

```text
A New Flow
```

Conceptually:

```text
Flow<User>
+
Flow<UserSettings>
↓
Flow<UserScreenUiModel>
```

Like other Flow operators:

```text
map()

filter()

debounce()
```

---

```text
combine()
```

does not immediately produce values.

Instead it creates:

```text
Another Flow
```

which can later be collected.

---

Example:

```kotlin
screenStateFlow.collect { uiModel ->

    render(uiModel)
}
```

---

## When Does combine() Start Emitting?

A common question appears:

```text
What If One Flow Emits Immediately
But Another Flow Has Not Emitted Yet?
```

Suppose:

```text
Flow A
```

emits:

```text
A1
```

immediately.

---

However:

```text
Flow B
```

does not emit until:

```text
10 Minutes Later
```

A natural question appears:

```text
Can combine() Emit Anything Yet?
```

The answer is:

```text
No
```

combine needs:

```text
At Least One Value From Every Flow
```

before it can produce its first combined result.

Conceptually:

```text
Latest A = A1

Latest B = ?
```

---

There is not enough information to produce:

```text
(A1, ?)
```

because:

```text
?
```

does not exist yet.

---

During those 10 minutes:

```text
combine()
↓
Waiting
```

---

Only after:

```text
B1
```

arrives does combine have:

```text
Latest A = A1

Latest B = B1
```

and can finally emit:

```text
(A1, B1)
```

This behavior is important to remember:

```text
combine() Cannot Start Until Every Flow Has Contributed At Least One Value
```

---

## What If One Flow Emits Many Times?

Consider:

```text
Flow A
```

emitting:

```text
A1
↓
A2
↓
A3
↓
A4
```

while:

```text
Flow B
```

has not emitted anything yet.

A natural question appears:

```text
Will combine() Eventually Emit:

(A1, B1)

(A2, B1)

(A3, B1)

(A4, B1)
```

when:

```text
B1
```

finally arrives?

The answer is:

```text
No
```

During this period:

```text
combine()
↓
Still Waiting
```

However it continues remembering:

```text
Latest A
```

Conceptually:

```text
Latest A = A1
```

↓

```text
Latest A = A2
```

↓

```text
Latest A = A3
```

↓

```text
Latest A = A4
```

---

When:

```text
B1
```

finally arrives:

```text
Latest A = A4

Latest B = B1
```

combine emits:

```text
(A4, B1)
```

Only the latest values matter.

---

## Understanding The Timeline

Suppose:

```text
userFlow
```

emits:

```text
U1
```

---

Later:

```text
settingsFlow
```

emits:

```text
S1
```

Now combine has:

```text
Latest User = U1

Latest Settings = S1
```

---

It can emit:

```text
(U1, S1)
```

---

Later:

```text
userFlow
```

emits:

```text
U2
```

---

combine remembers:

```text
Latest Settings = S1
```

and emits:

```text
(U2, S1)
```

---

Later:

```text
userFlow
```

emits:

```text
U3
```

---

combine emits:

```text
(U3, S1)
```

---

Later:

```text
settingsFlow
```

emits:

```text
S2
```

---

combine emits:

```text
(U3, S2)
```

---

Conceptually:

```text
Latest User
+
Latest Settings
↓
Emit
```

---

## What If One Flow Is Faster?

Suppose:

```text
Search Query Flow
```

emits:

```text
A
↓
AB
↓
ABC
↓
ABCD
```

very rapidly.

---

Meanwhile:

```text
Filter Settings Flow
```

emits:

```text
Settings V1
```

only once.

---

combine behaves like:

```text
Latest Query
+
Latest Settings
↓
Emit
```

---

Conceptually:

```text
(A, Settings V1)

(AB, Settings V1)

(ABC, Settings V1)

(ABCD, Settings V1)
```

---

The slow Flow does not prevent updates.

combine simply reuses:

```text
The Latest Value
```

it already knows.

---

## Why combine() Is So Useful

Many Android screens are really:

```text
Several Data Sources
```

merged together.

---

Examples:

```text
User
+
Settings
↓
Screen State
```

---

```text
Search Query
+
Filters
↓
Search Request
```

---

```text
Cart
+
Discount Information
↓
Checkout UI
```

---

In all of these situations:

```text
Latest State
```

is what matters.

This is exactly what:

```text
combine()
```

provides.

---

## Another Mental Model

Imagine a dashboard.

---

One widget updates:

```text
Temperature
```

---

Another updates:

```text
Humidity
```

---

Another updates:

```text
Wind Speed
```

---

Whenever any value changes, you want the dashboard to display:

```text
The Latest Information
Available
```

This is how:

```text
combine()
```

thinks.

---

## The Second Strategy: zip()

A natural question appears:

```text
What If I Don't Want Latest Values?
```

What if I want:

```text
First With First

Second With Second

Third With Third
```

instead?

This is where:

```text
zip()
```

becomes useful.

---

## Understanding zip()

Think of:

```text
zip()
```

as:

```text
Match Values By Position
```

---

Conceptually:

```text
First A
+
First B
↓
Emit
```

---

```text
Second A
+
Second B
↓
Emit
```

---

```text
Third A
+
Third B
↓
Emit
```

---

Unlike:

```text
combine()
```

zip does not use:

```text
Latest Values
```

It uses:

```text
Matching Pairs
```

---

## zip() Example

Consider:

```kotlin
val pairedFlow =
    flowA.zip(flowB) { a, b ->

        Pair(a, b)
    }
```

Suppose:

```text
Flow A
```

emits:

```text
A1

A2

A3
```

---

And:

```text
Flow B
```

emits:

```text
B1

B2

B3
```

---

zip produces:

```text
(A1, B1)

(A2, B2)

(A3, B3)
```

---

Values are paired by position.

---

## What If One Flow Is Faster?

Suppose:

```text
Flow A
```

emits:

```text
A1

A2

A3

A4

A5
```

very quickly.

---

Meanwhile:

```text
Flow B
```

has only emitted:

```text
B1
```

---

zip cannot produce:

```text
(A5, B1)
```

---

Instead:

```text
A2

A3

A4

A5
```

must wait for:

```text
B2

B3

B4

B5
```

to arrive.

---

Conceptually:

```text
A2
↓
Waiting
```

---

```text
B2 Arrives
↓
(A2, B2)
```

---

This synchronization behavior is the defining characteristic of:

```text
zip()
```

---

## combine() vs zip()

At first glance both appear to solve the same problem.

However, they answer different questions.

---

Think of:

```text
combine()
```

as:

```text
What Are The Latest Values?
```

---

Think of:

```text
zip()
```

as:

```text
What Are The Matching Pairs?
```

---

Conceptually:

```text
combine()
↓
Latest A
+
Latest B
```

---

```text
zip()
↓
First A
+
First B
```

---

This single distinction explains most of their behavior.

---

## When Should I Use combine()?

Common Android examples:

```text
UI State

Screen Models

Search Screens

Settings Screens

Dashboard Screens
```

---

Whenever you care about:

```text
The Latest State
```

combine is often the right choice.

---

In practice:

```text
combine()
```

is significantly more common in Android development.

---

## When Should I Use zip()?

Common examples:

```text
Pairing Records

Comparing Sequences

Processing Matching Items

Batch Data Operations
```

---

Whenever you care about:

```text
Matching Pairs
```

zip is often more appropriate.

---

## Another Common Misconception

A common misconception is:

```text
combine()
```

waits for every Flow to emit at exactly the same time.

Incorrect.

---

Flows remain:

```text
Independent
```

---

combine simply works with:

```text
The Latest Value From Each Flow
```

that it currently knows about.

---

## Another Common Misconception

A common misconception is:

```text
zip()
```

uses the latest values.

Incorrect.

---

zip pairs:

```text
First With First

Second With Second

Third With Third
```

regardless of when the values arrive.

---

## The Key Mental Model

Multiple Flows rarely emit at the same time.

---

The question is:

```text
How Should Independent Flows Be Synchronized?
```

---

```text
combine()
↓
Latest Values
```

---

```text
zip()
↓
Matching Pairs
```

Understanding this distinction explains why both APIs exist.

---

## Revision Table

| Section | Content |
|----------|----------|
| Type | Multi-Flow Operations |
| Main Problem | Data exists in multiple Flows |
| Core Challenge | Flows emit independently |
| First Strategy | combine() |
| combine() Uses | Latest values |
| combine() Returns | A new Flow |
| First Emission Rule | Needs one value from every Flow |
| Most Common Android Choice | combine() |
| Second Strategy | zip() |
| zip() Uses | Matching pairs |
| combine() Mental Model | Latest A + Latest B |
| zip() Mental Model | First A + First B |
| Previous Concept | Reducing Noise |
| Next Concept | Keeping Only The Latest Result |

---

## A Natural Question

We now understand:

```text
combine()
```

and:

```text
zip()
```

allow multiple Flows to work together.

A natural question appears:

```text
What Happens When New Values Make Previous Work Irrelevant?
```

Imagine a search screen.

The user types:

```text
A
```

and a search begins.

Before it finishes, the user types:

```text
AB
```

and another search begins.

Before that finishes, the user types:

```text
ABC
```

and another search begins.

A natural question appears:

```text
Should The Older Searches Continue?
```

Or should only the latest search matter?

This question leads us to our next concept.

---

For this chapter, focus on one idea:

```text
Multiple Flows Rarely Emit At The Same Time
```

---

```text
combine()
```

solves this by using:

```text
Latest Values
```

---

```text
zip()
```

solves this by using:

```text
Matching Pairs
```
