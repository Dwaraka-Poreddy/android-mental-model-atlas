# Response

## Observation

In the previous chapter,

we learned that communication begins when a client sends an HTTP request to a server.

A natural question appears.

Once the server receives the request,

how does it communicate the result back to the client?

---

## A Thought Experiment

Imagine you order a coffee at a café.

You walk to the counter and say:

> "I'd like a cappuccino."

At that moment,

you've made a request.

The conversation isn't complete yet.

The café now needs to respond.

Perhaps the barista says:

> "Sure, it'll be ready in five minutes."

Or maybe:

> "Sorry, we're out of milk."

Either way,

the conversation only becomes complete when you receive a response.

HTTP communication works exactly the same way.

---

## The Server Continues The Conversation

After receiving a request,

the server processes it.

Once it has finished,

it sends a **response** back to the client.

Conceptually,

the communication now looks like this.

```text
Client
   │
   │ Request
   ▼
Server
   │
   │ Response
   ▼
Client
```

A request begins the conversation.

A response completes it.

---

## What Is A Response?

An **HTTP Response** is a message sent from a server back to the client after processing a request.

The response tells the client what happened.

For example,

it might contain:

- the requested information,
- confirmation that an operation succeeded,
- or an explanation of why it failed.

The exact content isn't important yet.

The important idea is that every request eventually leads to a response.

---

## A Simple Example

Imagine your weather application asks for today's forecast.

Conceptually,

the conversation looks like this.

```text
Client
   │
   │ "What's today's weather?"
   ▼
Server
   │
   │ "27°C and Sunny"
   ▼
Client
```

The request asks.

The response answers.

---

## What Does A Response Contain?

A response is more than just data.

It also communicates information such as:

- whether the request succeeded,
- whether an error occurred,
- and any data the server wants to return.

We'll study each of these pieces separately in the upcoming chapters.

For now,

it's enough to know that a response carries everything the client needs to understand the result of its request.

---

## Connecting It To Android

Whenever your Android application calls:

```kotlin
val user = userApi.getUser()
```

your application eventually receives an HTTP response.

Retrofit then converts that response into Kotlin objects,

making it much easier to work with.

But underneath,

the communication is still based on a request followed by a response.

---

## The Bigger Picture

HTTP communication is always a conversation.

```text
Client
      │
      │ Request
      ▼
Server
      │
      │ Response
      ▼
Client
```

Every interaction follows this same pattern.

---

## Production Recognition

Whenever you receive:

```kotlin
Response<User>

Response<List<Product>>
```

don't immediately think:

> "That's a Retrofit class."

Instead think:

> "This represents the server's HTTP response."

The framework is simply giving your application access to that response.

---

## Revision

### Mental Model

```text
Client
      │
      │ Request
      ▼
Server
      │
      │ Response
      ▼
Client
```

### Remember

A response is the server's reply to a client's request.

Together,

a request and a response form a complete HTTP conversation.

---

## One Remaining Question

We've learned that clients send requests,

and servers send responses.

A natural question appears.

Are all requests the same?

Or can a client ask the server to perform different kinds of operations?

That leads us to the next concept.

```text
HTTP Methods
```
