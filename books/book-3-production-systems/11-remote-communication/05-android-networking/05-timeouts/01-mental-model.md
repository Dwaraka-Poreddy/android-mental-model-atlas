# Timeouts

## Observation

We've learned how Android applications communicate with remote servers.

Most of the time,

the server responds quickly.

A natural question appears.

What if the server never responds?

Or the network becomes extremely slow?

Should the application wait forever?

If not,

how does it decide when to stop waiting?

There must be a way to prevent requests from waiting indefinitely.

---

## A Thought Experiment

Imagine you've arranged to meet a friend at a café.

You arrive on time.

Five minutes pass.

Ten minutes.

Thirty minutes.

An hour.

At some point,

you stop waiting and leave.

Not because you know your friend isn't coming,

but because waiting forever isn't practical.

Applications face a very similar problem.

When sending a request,

they don't know whether:

- the server is slow,
- the network is congested,
- or the server will never respond.

Instead of waiting forever,

they decide on a reasonable time limit.

If that limit is exceeded,

the request fails.

---

## The Problem

Without a time limit,

a request could remain stuck indefinitely.

Conceptually,

it would look like this.

```text
Request

↓

Waiting...

↓

Waiting...

↓

Waiting...

↓

?
```

The application would never know whether to keep waiting or move on.

This leads to a poor user experience.

---

## Timeout

A **Timeout** is the maximum amount of time an application is willing to wait for a networking operation to complete.

If that time limit is exceeded,

the operation fails,

allowing the application to recover instead of waiting forever.

---

## Waiting Can Happen At Different Stages

A network request isn't a single operation.

It consists of several stages.

```text
Connect

↓

Send Request

↓

Receive Response
```

Each stage can become slow for different reasons.

Because of this,

networking libraries allow different timeout limits for different stages of communication.

---

## Connect Timeout

Before sending a request,

the application must first establish a connection with the server.

A natural question appears.

How long should it wait if the connection cannot be established?

This is the responsibility of the **Connect Timeout**.

---

## Read Timeout

Once the connection has been established,

the application waits for the server to send data.

If no data arrives,

how long should it continue waiting?

This is the responsibility of the **Read Timeout**.

---

## Write Timeout

Before receiving a response,

the application must first send its request.

If sending that data becomes unusually slow,

how long should it continue trying?

This is the responsibility of the **Write Timeout**.

---

## Connecting Everything Together

Every network request passes through multiple stages.

```text
Request

↓

Connect
    │
    └── Connect Timeout

↓

Send Request
    │
    └── Write Timeout

↓

Receive Response
    │
    └── Read Timeout

↓

Response
```

Each timeout protects one stage of the networking process,

ensuring that the application never waits forever.

---

## Revision

### Mental Model

```text
Request

↓

Connect

↓

Send

↓

Receive

↓

Response
```

Every stage has its own timeout.

### Remember

A timeout defines how long an application is willing to wait for a networking operation.

Instead of waiting forever,

the request fails once the configured time limit is exceeded,

allowing the application to recover gracefully.

---

## One Remaining Question

We've learned how applications avoid waiting forever.

A natural question appears.

Even with sensible timeouts,

network requests can still fail.

The device might be offline.

The server might return an error.

JSON parsing might fail.

How should Android applications handle all these different kinds of failures?

That leads us to the next concept.

```text
Error Handling
```
