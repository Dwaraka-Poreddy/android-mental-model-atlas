# HTTP Status Codes

## Observation

In the previous chapters,

we learned how a client constructs an HTTP request.

The request tells the server:

- what operation to perform,
- which resource to operate on,
- additional context,
- and any data it needs to process.

A natural question appears.

Once the server finishes processing the request,

how does it tell the client what happened?

Did everything succeed?

Was something missing?

Did an error occur?

The client needs a standard way to understand the outcome.

---

## A Thought Experiment

Imagine you submit an application at a government office.

After reviewing it,

the officer doesn't simply hand the papers back.

They usually communicate the result.

For example,

> "Your application has been approved."

or

> "Your application is incomplete."

or

> "We couldn't find your records."

Notice something important.

Before discussing any details,

the officer first tells you the overall outcome.

Computers need the same ability.

---

## The Server Needs To Communicate The Outcome

Whenever a server receives a request,

it processes it.

After processing,

it must tell the client what happened.

Without a standard way to communicate the result,

every application would invent its own response format.

The client would never know whether the request succeeded or failed.

HTTP solves this problem using **Status Codes**.

---

## HTTP Status Codes

An **HTTP Status Code** is a standardized number that tells the client the overall result of its request.

Think of it as a summary of the outcome.

Conceptually,

it looks like this.

```text
Client
      │
      │ Request
      ▼
Server
      │
      │ Process Request
      ▼
HTTP Status Code
      │
      ▼
Client Understands The Result
```

The status code is the server's first signal about what happened.

---

## Some Examples

A request might succeed.

Another request might ask for something that doesn't exist.

Another might fail because the client isn't authenticated.

Although the reasons are different,

the idea remains the same.

The server communicates the outcome using a status code.

We'll learn the individual status codes in the following chapters.

For now,

the important idea is simply that every HTTP response includes a standardized result.

---

## Connecting It To Android

Whenever your Retrofit call completes,

the underlying HTTP response includes a status code.

Retrofit exposes that information through classes such as:

```kotlin
Response<User>
```

This allows your application to determine whether the request succeeded before processing the returned data.

---

## The Bigger Picture

An HTTP conversation has two parts.

```text
Client
      │
      │ Request
      ▼
Server
      │
      │ Response
      ▼
Status Code
      │
      ▼
Client Understands The Outcome
```

The response may contain data,

but before the client interprets that data,

it first understands whether the request succeeded.

The status code communicates that outcome.

---

## Production Recognition

Whenever you see production code like:

```kotlin
if (response.isSuccessful) {
    ...
}
```

or

```kotlin
response.code()
```

don't think:

> "This is just checking a number."

Instead think:

> "The application is checking how the server says the request ended."

The status code summarizes the result of the request.

---

## Revision

### Mental Model

```text
Server Processes Request
        ↓
HTTP Status Code
        ↓
Client Understands The Outcome
```

### Remember

HTTP Status Codes don't contain the actual data.

They communicate the outcome of processing the request.

---

## One Remaining Question

We've learned that every response contains a status code.

A natural question appears.

What do the different status codes actually mean?

Why does the server sometimes return:

- 200,
- 404,
- 401,
- or 500?

That leads us to the next concept.

```text
Status Code Categories
```
