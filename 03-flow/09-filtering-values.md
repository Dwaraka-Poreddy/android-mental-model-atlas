# Filtering Values

## A Question From The Previous Chapter

In the previous chapter we learned:

```text
map()
```

allows us to:

```text
Transform Values
```

while they travel through the pipeline.

Conceptually:

```text
Input Value
↓
map()
↓
Output Value
```

A natural question appears:

```text
What If I Don't Want To Change The Value?

What If I Don't Want The Value At All?
```

Suppose a Flow produces:

```text
1

2

3

4

5
```

What if we only want:

```text
2

4
```

to continue through the pipeline?

How can unwanted values be removed?

This chapter answers that question.

---

## Revisiting Transformation

Recall:

```text
map()
```

changes values.

Conceptually:

```text
1
↓
map()
↓
10
```

The value survives.

Only its representation changes.

---

A natural question appears:

```text
What If Certain Values Should Never Reach The Collector?
```

This is a different problem.

Sometimes:

```text
Some Values Are Relevant
```

and:

```text
Some Values Are Not
```

---

## First Example

Imagine:

```text
1

2

3

4

5
```

moving through a pipeline.

Suppose we only care about:

```text
Even Numbers
```

Conceptually:

```text
2

4
```

should continue.

---

While:

```text
1

3

5
```

should disappear.

---

We need a way to:

```text
Keep Some Values

Remove Others
```

---

## The Missing Tool

Flow provides:

```text
filter()
```

for this purpose.

Think of:

```text
filter()
```

as:

```text
A Gate
```

through which values must pass.

---

Conceptually:

```text
Value Arrives
↓
Gate Checks Value
↓
Allow
```

or:

```text
Value Arrives
↓
Gate Checks Value
↓
Reject
```

---

Only approved values continue through the pipeline.

---

## First Code Example

Consider:

```kotlin
val numbers = flow {

    emit(1)

    emit(2)

    emit(3)

    emit(4)

    emit(5)
}
```

Using:

```kotlin
numbers.filter { value ->

    value % 2 == 0
}
```

Conceptually:

```text
1
↓
Rejected
```

---

```text
2
↓
Allowed
```

---

```text
3
↓
Rejected
```

---

```text
4
↓
Allowed
```

---

```text
5
↓
Rejected
```

The collector eventually receives:

```text
2

4
```

---

## What Does filter() Do?

Think of:

```text
filter()
```

as answering:

```text
Should This Value Continue?
```

---

Conceptually:

```text
Value
↓
filter()
↓
Yes
↓
Continue
```

---

or:

```text
Value
↓
filter()
↓
No
↓
Remove
```

---

Unlike:

```text
map()
```

which changes values,

```text
filter()
```

decides whether a value survives.

---

## First Mental Model

Imagine:

```text
Airport Security
```

People arrive at a checkpoint.

---

Conceptually:

```text
Person Arrives
↓
Inspection
↓
Allowed Through
```

or:

```text
Person Arrives
↓
Inspection
↓
Stopped
```

---

The checkpoint does not:

```text
Transform People
```

It simply decides:

```text
Who Continues
```

---

Flow filtering behaves similarly.

---

Conceptually:

```text
Value Arrives
↓
filter()
↓
Continue
```

or:

```text
Value Arrives
↓
filter()
↓
Removed
```

---

## A Real Android Example

Imagine:

```kotlin
Flow<User>
```

coming from a repository.

Not every user is active.

---

Suppose the UI only wants:

```text
Active Users
```

Example:

```kotlin
users.filter { user ->

    user.isActive
}
```

---

Conceptually:

```text
Inactive User
↓
Removed
```

---

```text
Active User
↓
Allowed Through
```

The collector only receives:

```text
Active Users
```

---

## Another Real Android Example

Imagine:

```kotlin
Flow<NetworkState>
```

---

The UI only cares when:

```text
Internet Is Available
```

Example:

```kotlin
networkStates.filter { state ->

    state.isConnected
}
```

---

Conceptually:

```text
Disconnected State
↓
Removed
```

---

```text
Connected State
↓
Allowed Through
```

Only useful states reach the collector.

---

## map() vs filter()

At first glance:

```text
map()
```

and:

```text
filter()
```

may appear similar because both operate inside the pipeline.

However, they solve different problems.

---

Conceptually:

```text
map()
↓
Change The Value
```

---

```text
filter()
↓
Decide Whether The Value Continues
```

---

Example:

```text
1
↓
map()
↓
10
```

The value survives.

---

Example:

```text
1
↓
filter()
↓
Removed
```

The value disappears.

---

## Relationship To The Pipeline

Earlier we learned:

```text
Producer
↓
Pipeline
↓
Collector
```

Now we can refine that model.

---

Conceptually:

```text
Producer
↓
filter()
↓
Collector
```

---

Only values that pass the filter continue moving through the pipeline.

---

Some values never reach the collector.

---

## Another Common Misconception

A common misconception is:

```text
filter() Changes Values
```

Incorrect.

---

Conceptually:

```text
Value In
↓
filter()
↓
Same Value Out
```

---

The value itself remains unchanged.

The only question is:

```text
Does It Continue?
```

---

## Another Mental Model

Imagine:

```text
A VIP Event
```

People arrive at the entrance.

---

Conceptually:

```text
Guest
↓
Check Invitation
↓
Allowed
```

---

```text
Guest
↓
No Invitation
↓
Rejected
```

---

The guest is not transformed.

The guest is either:

```text
Allowed
```

or:

```text
Rejected
```

---

This is exactly how:

```text
filter()
```

behaves.

---

## The Key Mental Model

Filtering means:

```text
Choosing Which Values Continue Through The Pipeline
```

---

Conceptually:

```text
Value
↓
filter()
↓
Continue
```

or:

```text
Value
↓
filter()
↓
Removed
```

---

Unlike:

```text
map()
```

which changes values,

```text
filter()
```

selects values.

---

## Revision Table

| Section | Content |
|----------|----------|
| Type | Flow Filtering |
| Main Problem | Not all values are useful |
| Solution | filter() |
| filter() Purpose | Decide which values continue |
| Value Changed? | No |
| Value Removed? | Yes |
| Mental Model | Security Checkpoint |
| Mental Model | VIP Event |
| Previous Concept | Transforming Values |
| Next Concept | Reducing Noise |

---

## A Natural Question

We now understand:

```text
filter()
```

allows us to remove:

```text
Unwanted Values
```

from a Flow.

A natural question appears:

```text
What If The Values Themselves Are Not The Problem?
```

Suppose every value is valid.

Suppose every value is useful.

Yet reacting to every value still creates problems.

Imagine a search box:

```text
H
↓
He
↓
Hel
↓
Hell
↓
Hello
```

Every value is meaningful.

None of them should be filtered out.

However:

```text
Do We Really Want To React To Every Keystroke?
```

Or would it be better to wait until the user pauses typing?

This introduces a different problem.

Not:

```text
Which Values Should Survive?
```

but:

```text
When Should We React To Values?
```

This question leads us to:

```text
Reducing Noise
```

which we will explore next.

---

For this chapter, focus on one idea:

```text
filter()
```

does not:

```text
Change Values
```

---

Instead it decides:

```text
Which Values Continue Through The Pipeline
```
