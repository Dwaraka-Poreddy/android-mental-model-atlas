# Web Service

## Observation

In the previous chapter,

we learned that software can expose a public interface called an API.

Other software interacts with that API,

without needing to know how the software is implemented internally.

A natural question appears.

What if the software exposing the API isn't running on our own computer?

Can software running on another computer expose its API too?

If so,

how can applications use it?

---

## A Thought Experiment

Imagine your calculator application.

It exposes operations like:

- Add
- Subtract
- Multiply
- Divide

If the calculator is installed on your computer,

other programs on the same computer can use its API directly.

Now imagine the calculator is moved to another computer on the internet.

The calculator still exposes the same API.

The only difference is that it's no longer running locally.

Applications now need to communicate over the network to use it.

The API hasn't changed.

Only its location has.

---

## The Problem

An API defines **what** software allows other software to do.

It doesn't define **where** that software is running.

If the software is running on another computer,

its API must somehow be made available across the network.

This is exactly what a Web Service does.

---

## Web Service

A **Web Service** is software that makes its API available over a network.

Conceptually,

it looks like this.

```text
Software
        │
        ▼
Public Interface (API)
        │
        ▼
Accessible Over A Network
        │
        ▼
Web Service
```

The API still defines the available operations.

The Web Service simply allows applications on other computers to use those operations.

---

## API vs Web Service

Although these terms are often used together,

they describe different ideas.

```text
API
        ↓
Public Interface

------------------------

Web Service
        ↓
Software That Exposes
Its API Over A Network
```

Every Web Service exposes an API.

But not every API is a Web Service.

For example,

the Android SDK exposes APIs.

The Kotlin Standard Library exposes APIs.

These APIs are available directly inside your application.

They are **not** Web Services.

On the other hand,

an online banking system exposes APIs that applications access over the internet.

That is a Web Service.

---

## Connecting It To Android

Every time your Android application communicates with:

- a payment system,
- a weather provider,
- an e-commerce platform,
- or a social media backend,

it is interacting with a Web Service.

The application doesn't access the server's internal implementation.

It uses the API that the Web Service exposes.

---

## The Bigger Picture

Notice how the concepts build on one another.

```text
API
        ↓
Public Interface

+

Network

↓

Web Service
```

A Web Service doesn't introduce a new kind of API.

It makes an existing API available to applications running on other computers.

---

## Production Recognition

When developers say:

- "Call the backend service."
- "The service returned an error."
- "The payment service is unavailable."

they're usually referring to a Web Service.

Behind the scenes,

that Web Service exposes an API that remote applications can use.

---

## Revision

### Mental Model

```text
API

+

Network

↓

Web Service
```

### Remember

A Web Service is software that exposes its API over a network,

allowing applications on different computers to use it.

---

## One Remaining Question

We've learned that a Web Service exposes an API over the network.

A natural question appears.

A single Web Service may expose many different operations.

How does a client specify exactly which operation it wants to use?

That leads us to the next concept.

```text
Endpoint
```
