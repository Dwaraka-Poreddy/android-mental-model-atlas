# Request

## Observation

In the previous chapter,

we learned that HTTP provides a common set of rules that allows clients and servers to communicate.

A natural question appears.

Once both computers understand HTTP,

how does the client actually ask for information?

---

## A Thought Experiment

Imagine you're standing at the reception desk of a hotel.

You don't simply stand there silently and expect a room key.

You make a request.

For example:

> "I'd like to check in."

Or:

> "Could I have today's breakfast menu?"

The receptionist now knows exactly what you're asking for.

Communication begins because someone made a request.

Computers work the same way.

---

## The Client Starts The Conversation

In HTTP,

communication almost always begins with the client.

The client sends a **request** to the server.

The request simply says:

> "Here's what I want."

Without a request,

the server has nothing to respond to.

---

## What Is A Request?

An **HTTP Request** is a message sent from a client to a server asking for something.

That "something" might be:

- user information,
- today's weather,
- a list of products,
- your bank balance,
- a login attempt.

The exact information doesn't matter.

The idea is always the same.

The client is asking the server to perform some action or provide some information.

---

## A Simple Example

Imagine your weather application needs today's forecast.

Conceptually,

the communication looks like this.

```text
Client
   │
   │ Request
   ▼
Server
```

At this point,

the server has received the request.

Its next job is to decide how to answer.

---

## What Does A Request Contain?

A request is more than just:

> "Give me something."

It needs to answer questions such as:

- Which resource do you want?
- What kind of operation are you performing?
- Are you sending any data?
- Who is making the request?

We'll study each of these pieces separately in the upcoming chapters.

For now,

it's enough to know that a request carries everything the server needs to understand what the client is asking.

---

## Connecting It To Android

Whenever your Android application performs operations such as:

```kotlin
userApi.getUser()

productApi.getProducts()

authApi.login()
```

your application is creating an HTTP request.

Retrofit helps you build that request,

but the underlying idea remains the same.

The client is asking the server for something.

---

## The Bigger Picture

HTTP communication always begins with a request.

```text
Client
      ↓
Request
      ↓
Server
```

Only after the request reaches the server can anything else happen.

---

## Production Recognition

Whenever you see code like:

```kotlin
@GET("/users")

@POST("/login")
```

don't immediately think:

> "This is a Retrofit annotation."

Instead think:

> "This code describes an HTTP request that my application will send."

The framework is simply helping you construct that request.

---

## Revision

### Mental Model

```text
Client
      ↓
Request
      ↓
Server
```

### Remember

A request is simply the client's way of asking the server for information or asking it to perform an action.

---

## One Remaining Question

We've learned how a client asks the server for something.

A natural question appears.

Once the server receives the request,

how does it communicate the result back to the client?

That leads us to the next concept.

```text
Response
```
