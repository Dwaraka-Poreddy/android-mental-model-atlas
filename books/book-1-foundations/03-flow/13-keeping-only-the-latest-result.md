# Keeping Only The Latest Result

## A Question From The Previous Chapter

In the previous chapter we learned that a transformation can look like:

```text
Value
↓
Flow
```

For example:

```text
Search Query
↓
Flow<SearchResults>
```

or:

```text
User Id
↓
Flow<User>
```

A natural question appears:

```text
What Happens When Every New Value Produces Another Flow?
```

Imagine a search screen.

The user types:

```text
android
```

creating:

```text
Flow<SearchResults>
```

Before those results finish updating, the user types:

```text
android flow
```

creating another:

```text
Flow<SearchResults>
```

Before that finishes, the user types:

```text
android flow combine
```

creating yet another:

```text
Flow<SearchResults>
```

Should every Flow continue?

Or should only the newest one matter?

This chapter answers that question.

---

## The Problem

Suppose we have:

```text
Search Query
↓
Search Results Flow
```

The user types:

```text
android
```

Conceptually:

```text
android
↓
Flow A
```

---

Immediately afterwards the user types:

```text
android flow
```

Conceptually:

```text
android flow
↓
Flow B
```

---

And again:

```text
android flow combine
```

Conceptually:

```text
android flow combine
↓
Flow C
```

Now we have:

```text
Flow A

Flow B

Flow C
```

all capable of producing values.

A natural question appears:

```text
Should They

All Continue?
```

---

## What If We Keep Everything?

Conceptually:

```text
Flow A
────────────────────────►

Flow B
──────────────────►

Flow C
──────────────►
```

Every Flow continues producing results.

At first this may seem acceptable.

However, consider what the user actually wants.

---

The user is currently searching for:

```text
android flow combine
```

Do they still care about:

```text
android
```

Probably not.

---

Or:

```text
android flow
```

Probably not.

---

Those older searches have become:

```text
Outdated
```

---

## A Real Android Problem

Imagine a slow network.

The user searches:

```text
a
```

---

A request begins.

Before it finishes, the user searches:

```text
android
```

Another request begins.

---

The second request finishes quickly:

```text
Results For android
```

and the UI updates.

---

Suddenly the first request finally finishes:

```text
Results For a
```

and replaces the correct results.

The user now sees:

```text
Old Information
```

instead of:

```text
The Latest Information
```

---

This is a very common problem in reactive applications.

---

## The Desired Behavior

Conceptually we really want:

```text
Flow A
──────X

Flow B
────X

Flow C
────────────────►
```

Whenever a newer Flow appears:

```text
Previous Flow

↓

No Longer Matters
```

Only the newest Flow should continue producing values.

---

## The Solution

Flow provides:

```text
flatMapLatest()
```

for exactly this situation.

Its idea is simple:

```text
Whenever A New Flow Appears Use It
```

and stop observing the previous one.

---

## First Example

```kotlin
val resultsFlow =
    searchQueryFlow.flatMapLatest { query ->

        repository.search(query)
    }
```

Conceptually:

```text
Search Query
↓
Search Flow
↓
Latest Search Results
```

---

Every new query produces:

```text
Another Flow
```

but only the latest Flow continues being observed.

---

## Timeline

Suppose:

```text
Query: android
```

creates:

```text
Flow A
```

---

Before Flow A finishes:

```text
Query: android flow
```

creates:

```text
Flow B
```

Conceptually:

```text
Flow A
──────X

Flow B
────────────►
```

---

Before Flow B finishes:

```text
Query: android flow combine
```

creates:

```text
Flow C
```

Conceptually:

```text
Flow A
──────X

Flow B
────X

Flow C
────────────────►
```

Only:

```text
Flow C
```

continues.

---

## Another Android Example

Imagine:

```text
Selected Chat Room
```

Conceptually:

```text
general
↓
Flow<Messages>
```

---

The user switches to:

```text
support
```

creating:

```text
Flow<Messages>
```

again.

Should messages from:

```text
general
```

continue updating the screen?

No.

Only:

```text
support
```

is now relevant.

---

## Another Android Example

Imagine:

```text
Selected Product
```

Conceptually:

```text
Product Id
↓
Flow<Product>
```

If the user quickly switches between products:

```text
Product A

Product B

Product C
```

only:

```text
Product C
```

should continue updating the UI.

---

## Another Mental Model

Imagine listening to the radio.

You tune into:

```text
Station A
```

Music begins playing.

---

You now switch to:

```text
Station B
```

Do you continue listening to:

```text
Station A
```

and:

```text
Station B
```

at the same time?

No.

You naturally begin listening only to:

```text
The Latest Station
```

Conceptually:

```text
Select New Station
↓
Stop Listening To Previous
↓
Listen To Latest
```

This is very similar to:

```text
flatMapLatest()
```

---

## Another Common Misconception

A common misconception is:

```text
Every Produced Flow Should Continue Forever
```

In many situations this would produce:

```text
Unnecessary Work Outdated Results Resource Waste
```

Often:

```text
Only The Latest Flow
```

is meaningful.

---

## Another Common Misconception

A common misconception is:

```text
flatMapLatest() Produces Better Results
```

Incorrect.

The produced values remain the same.

What changes is:

```text
Which Flow Is Currently Observed
```

---

## The Key Mental Model

Sometimes:

```text
New Information Makes Previous Work Irrelevant
```

When this happens:

```text
Old Flow

↓

Ignore
```

---

```text
Latest Flow

↓

Continue Observing
```

This is exactly the behavior provided by:

```text
flatMapLatest()
```

---

## Revision Table

| Section | Content |
|----------|----------|
| Main Problem | New Flows make previous Flows irrelevant |
| Pattern | Value → Flow |
| Desired Behavior | Observe only the latest Flow |
| Solution | flatMapLatest() |
| Mental Model | Switch to the latest radio station |
| Common Android Examples | Search, Chat, Product Selection |
| Previous Concept | From Values To Flows |
| Next Concept | Terminal Operators |

---

## A Natural Question

So far we have built many Flow pipelines:

```text
map()

filter()

debounce()

combine()

flatMapLatest()
```

A natural question appears:

```text
When Does Any Of This Actually Produce Values?
```

Creating a Flow:

```text
Does Not Execute It
```

Adding operators:

```text
Does Not Execute It
```

So what finally causes the entire pipeline to run?

This question leads us to:

```text
Terminal Operators
```

which we will explore next.

---

For this chapter, focus on one idea:

```text
Sometimes The Latest Information Is The Only Information That Still Matters.
```

In those situations:

```text
flatMapLatest()
```

helps us continue observing only:

```text
The Latest Flow.
```
