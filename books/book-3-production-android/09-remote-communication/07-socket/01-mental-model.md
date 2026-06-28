# Socket

## Observation

In the previous chapter,

we learned that a port identifies the application a client wants to communicate with.

A natural question appears.

Knowing the correct computer and the correct application isn't enough.

How does the client actually begin exchanging data with that application?

There needs to be a communication channel between them.

---

## A Thought Experiment

Imagine making a phone call.

Knowing someone's:

- city,
- street,
- apartment number,

doesn't automatically let you talk to them.

You still need to establish the phone call.

Only after the call is connected can both people begin speaking.

Computers have the same requirement.

Knowing the destination isn't enough.

A communication channel must first be established.

---

## Socket

A **Socket** is one endpoint of a communication channel between two applications.

Think of it as one end of a conversation.

The client owns one socket.

The server owns another.

Together,

they allow data to flow between the two applications.

Conceptually,

it looks like this.

```text
Client Application
        │
     Socket
        │
────────┼────────
        │
     Socket
        │
Server Application
```

Neither socket is useful on its own.

Communication happens only when both endpoints are connected.

---

## Why Sockets Exist

Imagine trying to send data directly to an application without creating a communication channel.

The operating system wouldn't know:

- where the conversation starts,
- where it ends,
- or which incoming data belongs to which ongoing conversation.

Sockets solve this problem.

Each communication between a client and a server uses its own pair of sockets.

This allows many conversations to happen at the same time without interfering with one another.

---

## Connecting It To Android

When your Android application makes an HTTP request,

libraries such as **OkHttp** create sockets behind the scenes.

Most Android developers never create sockets manually.

Instead,

networking libraries manage them automatically.

Even though you rarely see sockets in application code,

every HTTP request ultimately travels through one.

---

## The Bigger Picture

Network communication builds layer by layer.

```text
IP Address
        ↓
Which Computer?

------------------------

Port
        ↓
Which Application?

------------------------

Socket
        ↓
Communication Endpoint
```

A socket is the endpoint through which data enters and leaves an application.

---

## Production Recognition

Most Android developers won't see sockets directly.

Instead,

they'll encounter higher-level networking libraries.

Whenever you use:

```kotlin
Retrofit

OkHttp
```

remember:

> "These libraries eventually communicate through sockets."

Sockets are one of the fundamental building blocks hidden beneath modern networking APIs.

---

## Revision

### Mental Model

```text
Client Application
        │
     Socket
        │
────────┼────────
        │
     Socket
        │
Server Application
```

### Remember

A socket is one endpoint of a communication channel between two applications.

Communication happens when a client's socket and a server's socket are connected.

---

## One Remaining Question

We've learned that communication happens through sockets.

A natural question appears.

What happens when those two sockets are successfully linked together?

That leads us to the next concept.

```text
Connection
```
