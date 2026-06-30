# OkHttp

## Observation

So far,

we've learned how computers communicate across a network.

We now understand:

- HTTP
- Requests
- Responses
- Headers
- Bodies
- JSON
- DTOs

A natural question appears.

If an Android application wants to send an HTTP request,

does it have to:

- open a socket,
- establish a connection,
- construct the HTTP request,
- send bytes across the network,
- wait for the response,
- receive the bytes,
- parse the response,
- and finally close the connection,

every single time?

That would be an enormous amount of work.

There must be a better way.

---

## A Thought Experiment

Imagine you need to send a package to another city.

One option is to:

- buy a truck,
- hire a driver,
- plan the route,
- maintain the vehicle,
- deliver the package yourself.

Technically,

you could do all of that.

But it would be an enormous amount of work just to send one package.

Instead,

you simply hand the package to a courier company.

The courier handles:

- transportation,
- routing,
- delivery,
- and all the logistics involved.

You only describe **what** you want to send.

The courier takes care of **how** it reaches the destination.

OkHttp plays a very similar role.

Your application prepares an HTTP request.

OkHttp takes responsibility for delivering it and bringing the response back.

---

## The Problem

Without a networking library,

every HTTP request would require the application to manage many low-level details.

Conceptually,

the process looks like this.

```text
Create Socket

↓

Establish Connection

↓

Construct HTTP Request

↓

Attach Headers

↓

Attach Body

↓

Send Bytes

↓

Receive Bytes

↓

Parse HTTP Response

↓

Release Resources
```

Every one of these steps is necessary.

Fortunately,

your application doesn't need to implement them itself.

---

## OkHttp

**OkHttp** is an HTTP client library for Android and Java.

Its responsibility is to perform HTTP communication on behalf of your application.

Conceptually,

it sits between your application and the network.

```text
Application

↓

OkHttp

↓

HTTP

↓

Server
```

Notice something important.

OkHttp does **not** replace HTTP.

HTTP is still the communication protocol.

OkHttp is simply a library that implements that protocol for your application.

---

## What OkHttp Does

When your application wants to communicate with a server,

it creates an HTTP request.

OkHttp then takes responsibility for tasks such as:

- opening connections,
- sending requests,
- receiving responses,
- following redirects,
- managing resources,
- reusing connections when possible.

Instead of worrying about these low-level details,

your application focuses on describing the request.

OkHttp handles the communication.

---

## Looking Behind One Line Of Code

In production Android applications,

you'll often encounter code like this.

```kotlin
val response = client
    .newCall(request)
    .execute()
```

At first,

this appears to be a simple function call.

But now you know what it represents.

```text
Build Request

↓

Open Connection

↓

Send HTTP Request

↓

Wait For Response

↓

Receive Response

↓

Release Resources

↓

Return Result
```

One line of code hides a remarkable amount of work.

This is the power of abstraction.

---

## Connecting Everything We've Learned

One of the goals of this book has been to build networking from first principles.

Notice how every concept we've learned still exists.

```text
Network

↓

IP Address

↓

DNS

↓

Socket

↓

Connection

↓

HTTP

↓

Request

↓

Response
```

OkHttp doesn't replace any of these concepts.

It simply automates them,

allowing Android developers to focus on building applications instead of implementing networking from scratch.

---

## Revision

### Mental Model

```text
Application

↓

OkHttp

↓

HTTP

↓

Server
```

### Remember

OkHttp is an HTTP client.

It doesn't change how networking works.

It performs the low-level work required to send HTTP requests and receive HTTP responses.

---

## One Remaining Question

We've learned that OkHttp can perform HTTP requests for our application.

A natural question appears.

If OkHttp already knows how to communicate with servers,

why do almost all Android applications also use Retrofit?

What additional problem does Retrofit solve?

That leads us to the next concept.

```text
Retrofit
```
