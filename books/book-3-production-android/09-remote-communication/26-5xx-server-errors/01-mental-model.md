# 5xx Server Errors

## Observation

In the previous chapter,

we learned that **4xx** status codes indicate problems with the client's request.

A natural question appears.

What if the client sends a perfectly valid request,

but something goes wrong while the server is processing it?

For example,

- the server crashes,
- the database is unavailable,
- or another internal service stops responding.

How does the server communicate these situations?

---

## A Thought Experiment

Imagine you visit a restaurant.

You place a perfectly valid order.

The waiter understands exactly what you want.

The kitchen begins preparing your meal.

But halfway through,

the oven suddenly stops working.

Notice something important.

You didn't make a mistake.

The restaurant understood your request.

The problem happened entirely inside the restaurant.

HTTP makes the same distinction.

---

## What Server Errors Really Mean

Whenever a server returns a **5xx** status code,

it is telling the client:

> "Your request was valid,

but I couldn't complete it because something went wrong on my side."

The important idea is:

the problem is on the **server side**.

---

## Common Server Errors

Although every **5xx** response represents a server-side problem,

different status codes describe different situations.

### 500 Internal Server Error

```text
Unexpected Error
Inside The Server
```

The server encountered an unexpected problem while processing the request.

This is the most common server error.

---

### 502 Bad Gateway

```text
Server
        ↓
Couldn't Get A Valid Response
From Another Server
```

Sometimes,

one server depends on another service.

If that communication fails,

a **502 Bad Gateway** response may be returned.

---

### 503 Service Unavailable

```text
Server
        ↓
Temporarily Unable
To Handle Requests
```

The server is currently unavailable.

For example,

it may be:

- overloaded,
- undergoing maintenance,
- or temporarily offline.

Unlike a **500** error,

the server may become available again shortly.

---

## Connecting It To Android

When your Android application receives a **5xx** response,

retrying the request later may succeed.

Unlike many **4xx** errors,

the client usually cannot fix the problem immediately.

The issue exists on the server.

Applications often respond by showing messages such as:

> "Something went wrong. Please try again later."

---

## The Bigger Picture

Every **5xx** response communicates the same core idea.

```text
Client Sent
A Valid Request
        ↓
Problem Exists
Inside The Server
        ↓
        5xx
```

The specific status code explains what kind of server-side problem occurred.

---

## Production Recognition

When reading production code,

reason like this.

```text
5xx
    ↓
Server Problem

↓

Which Kind?
```

Then use the specific status code to understand the exact issue.

---

## Revision

### Mental Model

```text
Client Sent
A Valid Request
        ↓
Problem Exists
Inside The Server
        ↓
        5xx
```

### Common Server Errors

```text
500
    ↓
Unexpected Server Error

------------------------

502
    ↓
Communication Failure
Between Servers

------------------------

503
    ↓
Server Temporarily
Unavailable
```

### Remember

A **5xx** response means the client's request was valid,

but the server couldn't complete it because of an internal problem.

---

## One Remaining Question

We've learned how clients and servers communicate,

how requests are structured,

and how servers communicate success and failure.

A natural question appears.

If HTTP messages travel across the internet,

what prevents someone else from reading or modifying them while they're in transit?

That leads us to the next concept.

```text
HTTPS
```
