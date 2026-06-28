# From Values To Flows

## A Question From The Previous Chapter

In the previous chapters we learned that Flow operators usually transform:

```text
Value
↓
Value
```

For example:

```text
map()
```

conceptually performs:

```text
User
↓
UserUiModel
```

---

```text
filter()
```

conceptually performs:

```text
User
↓
User

or

User
↓
Removed
```

---

```text
debounce()
```

conceptually performs:

```text
User
↓
Same User (Later)
```

A natural question appears:

```text
Can A Value Become An Entire Flow?
```

At first this may sound strange.

However, many real Android applications work exactly this way.

This chapter explores that idea.

---

## A Different Kind Of Transformation

So far every transformation has looked like:

```text
Value
↓
Value
```

Now consider a different situation.

Suppose we have:

```text
User Id
```

For example:

```text
"12345"
```

A natural question appears:

```text
How Do We Observe This User?
```

The answer is often:

```kotlin
observeUser(userId)
```

Notice what this returns:

```kotlin
Flow<User>
```

Conceptually:

```text
User Id
↓
Observe User
↓
Flow<User>
```

---

The output is no longer:

```text
Another Value
```

Instead the output is:

```text
Another Flow
```

---

## Why Doesn't It Return A User?

A natural question appears:

```text
Why Not Just Return User?
```

Instead of:

```text
Flow<User>
```

Imagine:

```text
User Name Changes

Profile Picture Changes

Status Changes
```

These changes can happen over time.

If:

```kotlin
observeUser(userId)
```

returned only:

```kotlin
User
```

we would receive:

```text
One Snapshot
```

and nothing more.

---

Returning:

```text
Flow<User>
```

allows us to receive:

```text
Initial User
↓
Updated User
↓
Updated User
↓
Updated User
```

as changes happen.

---

## Another Android Example

Consider:

```text
Chat Id
```

For example:

```text
general-room
```

A repository might expose:

```kotlin
observeMessages(chatId)
```

returning:

```kotlin
Flow<List<Message>>
```

Conceptually:

```text
Chat Id
↓
Observe Messages
↓
Flow<List<Message>>
```

---

Every new message naturally appears as another emission.

---

Again:

```text
Value
↓
Flow
```

---

## Search Is Another Example

Suppose we have:

```text
Search Query
```

For example:

```text
android
```

A repository might expose:

```kotlin
search(query)
```

returning:

```kotlin
Flow<SearchResults>
```

Conceptually:

```text
Search Query
↓
Observe Search Results
↓
Flow<SearchResults>
```

---

As results update:

```text
Loading
↓
Partial Results
↓
Complete Results
```

the Flow continues producing values.

---

## A Common Pattern

Many Android architectures follow the same pattern.

```text
Some Identifier
↓
Observe Something
↓
Flow<Something>
```

---

Examples:

```text
User Id
↓
Flow<User>
```

---

```text
Chat Id
↓
Flow<List<Message>>
```

---

```text
Product Id
↓
Flow<Product>
```

---

```text
Search Query
↓
Flow<SearchResults>
```

---

Notice the pattern.

The input is:

```text
One Value
```

The output is:

```text
One Flow
```

---

## Another Mental Model

Imagine a TV remote.

The remote holds:

```text
Channel Number
```

For example:

```text
101
```

---

Selecting:

```text
101
```

does not give you:

```text
One Frame
```

Instead it connects you to:

```text
A Continuous Broadcast
```

Conceptually:

```text
Channel Number
↓
Television Channel
↓
Continuous Stream
```

---

Similarly:

```text
User Id
```

does not necessarily produce:

```text
One User
```

It may produce:

```text
A Continuous Stream Of User Updates
```

---

## Code Example

```kotlin
val userFlow =
    repository.observeUser(userId)

userFlow.collect { user ->

    render(user)
}
```

Conceptually:

```text
User Id
↓
observeUser()
↓
Flow<User>
↓
collect()
↓
Render Updates
```

---

## Another Code Example

```kotlin
val messagesFlow =
    repository.observeMessages(chatId)

messagesFlow.collect { messages ->

    showMessages(messages)
}
```

Every time new messages arrive:

```text
Flow
↓
Collector
↓
UI Updates
```

---

## What Has Changed?

Earlier chapters taught:

```text
Value
↓
Value
```

This chapter introduces:

```text
Value
↓
Flow
```

This is a new abstraction.

Instead of producing:

```text
One Answer
```

a value can identify:

```text
Something That Changes Over Time
```

making a Flow the natural result.

---

## Another Common Misconception

A common misconception is:

```text
Returning A Flow Means Multiple Values Must Always Exist
```

Incorrect.

A Flow may emit:

```text
One Value
```

or:

```text
Many Values
```

The important idea is that:

```text
The Possibility Of Future Updates Exists
```

---

## The Key Mental Model

Sometimes a value represents:

```text
Something Static
```

and returning:

```text
One Value
```

is sufficient.

---

Sometimes a value represents:

```text
Something That Changes Over Time
```

In those situations:

```text
Value
↓
Flow
```

becomes a natural transformation.

---

## Revision Table

| Section | Content |
|----------|----------|
| Main Idea | A value can naturally produce a Flow |
| Previous Pattern | Value → Value |
| New Pattern | Value → Flow |
| Common Android Examples | User, Chat, Product, Search |
| Why Return Flow? | Future updates |
| Mental Model | Channel Number → Continuous Broadcast |
| Previous Concept | Combining Flows |
| Next Concept | Keeping Only The Latest Result |

---

## A Natural Question

We now understand:

```text
One Value
```

can naturally produce:

```text
One Flow
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

Should both Flows continue?

Or should only the newest one matter?

This question leads us to:

```text
Keeping Only The Latest Result
```

which we will explore next.

---

For this chapter, focus on one idea:

```text
Not Every Transformation Is

Value
↓

Value
```

Sometimes the most natural transformation is:

```text
Value
↓

Flow
```

and this pattern appears throughout modern Android architecture.
