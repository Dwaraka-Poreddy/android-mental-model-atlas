# Network Result

## Observation

We've learned that network requests don't always succeed.

Sometimes,

they fail because of:

- no internet,
- server errors,
- timeouts,
- invalid responses,
- and many other reasons.

A natural question appears.

How should a networking layer communicate the outcome of a request to the rest of the application?

One possible approach is to simply return the requested data.

```kotlin
suspend fun getProfile(): UserDto
```

But what happens if the request fails?

There must be a better way.

---

## A Thought Experiment

Imagine receiving your university exam results.

The university doesn't simply send you your marks.

Nor does it send nothing if you fail.

Instead,

it sends one result.

Conceptually,

```text
Exam Result

↓

Passed

or

Failed
```

If you passed,

the result contains your marks.

If you failed,

the result explains that the exam wasn't successful.

Every student receives one result.

Only the outcome differs.

Production Android applications often communicate network requests in a very similar way.

Every request produces exactly one result.

Either it succeeds,

or it fails.

---

## The Problem

Simply returning data isn't enough.

```kotlin
suspend fun getProfile(): UserDto
```

If the request succeeds,

everything is fine.

But if it fails,

there is no obvious way to represent that outcome.

Other approaches aren't much better.

Returning `null`

doesn't explain why the request failed.

Returning a `Boolean`

only tells us whether the operation succeeded.

Neither approach communicates the complete outcome of the request.

---

## Network Result

Instead of exposing only the requested data,

production applications often return a **Network Result**.

Conceptually,

every request produces one of two outcomes.

```text
Network Result

├── Success

└── Error
```

A request can never be both.

And it should never produce neither.

Every network operation finishes with exactly one well-defined result.

---

## Success

When the request succeeds,

the result contains the requested data.

Conceptually,

```text
Network Result

↓

Success

↓

Requested Data
```

The rest of the application can safely continue using that data.

---

## Error

When the request fails,

the result represents that failure instead.

Conceptually,

```text
Network Result

↓

Error

↓

Failure Information
```

At this point,

we're only concerned with the idea that the request failed.

We'll learn how production applications represent different kinds of failures in the next chapter.

---

## One Result, Many Data Types

Different network requests return different kinds of data.

For example,

```text
Get Profile

↓

User
```

---

```text
Get Orders

↓

List<Order>
```

---

```text
Get Payment

↓

Payment
```

Although the returned data changes,

the overall structure remains exactly the same.

```text
Network Result

├── Success(Data)

└── Error(...)
```

Only the success data changes.

The idea of representing the outcome stays consistent across every request.

---

## Why This Scales Better

Without a common result type,

every API may communicate failures differently.

Some might throw exceptions.

Some might return `null`.

Some might return empty lists.

Some might return booleans.

The rest of the application would need to understand every one of those approaches.

A Network Result gives the application one consistent contract for every network request.

Instead of asking,

> "How does this API report failures?"

the caller only asks,

> "Was this request successful?"

---

## Connecting Everything Together

The networking flow now looks like this.

```text
Application

↓

Remote Data Source

↓

Network Result

├── Success

└── Error
```

The rest of the application no longer needs to know how networking libraries communicate failures.

It simply receives a well-defined result.

---

## Revision

### Mental Model

```text
Request

↓

Network Result

├── Success

└── Error
```

### Remember

A Network Result provides one consistent way for the networking layer to communicate the outcome of every request.

Instead of exposing only successful data,

it represents both successful and unsuccessful outcomes using the same contract.

---

## One Remaining Question

We've learned that every network request should produce a well-defined result.

A natural question appears.

What exactly should the **Error** contain?

Should it expose low-level exceptions thrown by networking libraries?

Or should production applications represent failures in a cleaner,

application-friendly way?

That leads us to the next concept.

```text
Network Error Modeling
```
