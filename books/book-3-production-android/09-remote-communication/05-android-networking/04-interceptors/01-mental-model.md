# Interceptors

## Observation

We've now built a complete networking pipeline.

Our application can:

- describe APIs using Retrofit,
- convert data using Converter Factories,
- perform HTTP communication using OkHttp.

A natural question appears.

Imagine every request sent by our application must include an authentication token.

Should every API call manually add it?

```kotlin
getProfile(token)

getOrders(token)

transferMoney(token)

getNotifications(token)
```

That would quickly become repetitive.

And if we ever changed how authentication worked,

we'd have to update every API call in the application.

There must be a better way.

---

## A Thought Experiment

Imagine a large office building with many departments.

Every morning,

employees report to different departments.

```text
Employee

↓

Reception Desk

↓

Department
```

Each department could verify every employee's identity before allowing them to enter.

```text
HR verifies employees.

Engineering verifies employees.

Finance verifies employees.

Support verifies employees.
```

Technically,

this would work.

But every department would be repeating exactly the same verification process.

Instead,

the building has a reception desk.

Every employee passes through the reception once.

The receptionist verifies their identity,

and only then allows them to continue to their department.

The departments no longer need to repeat the same work.

Interceptors solve a very similar problem.

Instead of every API call repeatedly adding authentication,

logging,

or common headers,

every HTTP request passes through an interceptor first.

The interceptor performs those common tasks once,

before allowing the request to continue.

---

## The Problem

Without interceptors,

common networking tasks become repetitive.

Imagine every request needs the same authentication header.

```text
Request 1

↓

Add Authentication Header

↓

Send

------------------------

Request 2

↓

Add Authentication Header

↓

Send

------------------------

Request 3

↓

Add Authentication Header

↓

Send
```

The same work is repeated for every request.

This makes the application harder to maintain.

---

## Interceptor

An **Interceptor** is a component that can inspect or modify every HTTP request and response as it passes through the networking pipeline.

Conceptually,

it sits between the application and the network.

```text
Application

↓

Interceptor

↓

OkHttp

↓

HTTP

↓

Server
```

Instead of adding common behavior to every API call,

the behavior is written once inside the interceptor.

Every request automatically benefits from it.

---

## Requests And Responses

An important detail is that interceptors don't only see requests.

They also see responses.

Conceptually,

the complete flow looks like this.

```text
Request

↓

Interceptor

↓

Server

↓

Interceptor

↓

Response
```

This allows an interceptor to work with both outgoing requests and incoming responses.

---

## What Can An Interceptor Do?

Interceptors are commonly used to:

- attach authentication tokens,
- add common headers,
- log requests,
- log responses,
- measure request duration,
- retry failed requests,
- inspect or modify responses.

Notice something important.

An interceptor isn't responsible for only one task.

It provides a place where common networking behavior can be applied consistently to every request and response.

---

## Multiple Interceptors

Production applications rarely have only one interceptor.

Instead,

multiple interceptors work together.

Conceptually,

the request may pass through several interceptors before reaching the server.

```text
Application

↓

Authentication Interceptor

↓

Logging Interceptor

↓

Analytics Interceptor

↓

OkHttp

↓

HTTP

↓

Server
```

Each interceptor has a single responsibility,

making the networking pipeline easier to understand and maintain.

---

## Connecting Everything Together

Our networking pipeline now looks like this.

```text
Application

↓

Retrofit

↓

Converter Factory

↓

Interceptor

↓

OkHttp

↓

HTTP

↓

Server
```

Each layer performs a different responsibility.

- The application decides **what** it wants.
- Retrofit describes the API.
- Converter Factories translate data.
- Interceptors apply common networking behavior.
- OkHttp performs HTTP communication.
- HTTP defines the communication protocol.

Together,

they create a flexible and maintainable networking stack.

---

## Revision

### Mental Model

```text
Application

↓

Interceptor

↓

OkHttp

↓

HTTP

↓

Server
```

### Remember

An interceptor allows applications to apply common behavior to every HTTP request and response.

Instead of repeating the same networking logic throughout the application,

that logic is written once and automatically applied to all requests.

---

## One Remaining Question

We've learned that interceptors can inspect and modify every request and response.

A natural question appears.

What happens if the server never responds?

Or the network becomes extremely slow?

Should the application wait forever?

How does it decide when to stop waiting?

That leads us to the next concept.

```text
Timeouts
```
