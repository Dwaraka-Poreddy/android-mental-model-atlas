# HTTP

## Observation

In the previous chapters,

we learned:

- how computers communicate through networks,
- how clients request information from servers,
- how servers are identified using IP addresses,
- and how domain names are translated into IP addresses using DNS.

A natural question appears.

Once a client finds the correct server,

how do the two computers actually communicate?

---

## A Thought Experiment

Imagine two people who don't share a common language.

One speaks only English.

The other speaks only Japanese.

Even if they're standing next to each other,

communication becomes difficult.

The problem isn't the distance.

The problem is that they don't share the same language.

Computers face the same challenge.

Simply connecting two computers isn't enough.

They also need a common set of rules for communication.

---

## Why Agreeing On Rules Matters

Imagine one computer sends:

> "Give me today's weather."

But the other computer expects every request to begin with:

> "REQUEST WEATHER"

Another computer might expect:

> "WEATHER PLEASE"

Without agreed rules,

every pair of computers would need to invent their own way of communicating.

That would make the Internet impossible to scale.

There needed to be one common language that everyone understood.

---

## HTTP

**HTTP** stands for:

> **HyperText Transfer Protocol**

The important word isn't "HyperText."

The important word is:

> **Protocol**

A protocol is simply an agreed set of rules for communication.

Think of it like this.

```text
Two Computers
      ↓
Need Common Rules
      ↓
Protocol
```

HTTP is one such protocol.

It defines how a client and a server exchange information over the web.

---

## What HTTP Does

HTTP doesn't decide:

- which server to contact,
- how to find an IP address,
- or what information exists.

Those problems have already been solved.

HTTP answers a different question.

> **Now that the client has reached the correct server, how should they communicate?**

---

## Connecting It To Android

Whenever your Android application fetches:

- user information,
- products,
- weather,
- account balances,

it is almost always communicating using HTTP.

Libraries such as Retrofit and OkHttp don't replace HTTP.

They simply make it easier for Android developers to use HTTP correctly.

---

## The Bigger Picture

Every concept we've learned so far builds toward HTTP.

```text
Network
      ↓
Client & Server
      ↓
IP Address
      ↓
Domain Name
      ↓
DNS
      ↓
HTTP
```

Each concept solved one problem.

HTTP solves the next one.

It gives every client and every server a common language.

---

## Production Recognition

Whenever you see:

```kotlin
Retrofit.Builder()

OkHttpClient()

@GET("/users")
```

don't immediately think:

> "Retrofit is talking to the server."

Instead think:

> "My application is communicating using HTTP."

Retrofit and OkHttp are simply tools that implement the HTTP protocol.

---

## Revision

### Mental Model

```text
Two Computers
      ↓
Need Common Rules
      ↓
HTTP
```

### Remember

HTTP is not a library.

It is not an Android framework.

It is not a server.

It is a communication protocol that defines how clients and servers exchange information.

---

## One Remaining Question

We've learned that HTTP provides the rules for communication.

A natural question appears.

If a client wants some information,

how does it actually ask for it using HTTP?

That leads us to the next concept.

```text
Request
```
