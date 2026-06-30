# Retrofit

## Observation

In the previous chapter,

we learned that OkHttp performs HTTP communication on behalf of our application.

A natural question appears.

If OkHttp can already send HTTP requests,

why do almost all Android applications also use Retrofit?

Could we build an entire application using only OkHttp?

Yes.

But for applications with dozens or even hundreds of API endpoints,

manually constructing every request quickly becomes repetitive.

There must be a better abstraction.

---

## A Thought Experiment

Imagine ordering food from a restaurant.

One way would be to tell the restaurant every single detail.

```text
Restaurant Address

↓

Kitchen

↓

Menu Code

↓

Delivery Address

↓

Payment Details

↓

Place Order
```

Technically,

this works.

But every order requires repeating the same process.

Instead,

a food ordering application lets you simply choose:

```text
Margherita Pizza
```

Behind the scenes,

the application fills in all the repetitive details for you.

Retrofit plays a very similar role.

Instead of manually constructing every HTTP request,

you simply describe the API you want to call.

Retrofit handles the repetitive work.

---

## The Problem

Without Retrofit,

every API request requires manually building an HTTP request.

Conceptually,

it looks like this.

```text
Choose HTTP Method

↓

Build URL

↓

Add Headers

↓

Add Body

↓

Create Request

↓

Execute Request

↓

Receive Response
```

Repeating these steps for every API quickly becomes tedious and error-prone.

---

## Retrofit

**Retrofit** is a library that allows developers to describe APIs instead of manually constructing HTTP requests.

Rather than building every request by hand,

developers define the API once.

After that,

calling an API feels like calling a normal Kotlin function.

Conceptually,

Retrofit sits on top of OkHttp.

```text
Application

↓

Retrofit

↓

OkHttp

↓

HTTP

↓

Server
```

Retrofit doesn't replace OkHttp.

It uses OkHttp to perform the actual HTTP communication.

---

## What Retrofit Does

Retrofit helps developers describe an API in terms of:

- endpoints,
- HTTP methods,
- request parameters,
- request bodies,
- expected responses.

Instead of manually creating an HTTP request every time,

the application simply calls a function representing that API.

This removes a large amount of repetitive networking code.

---

## Describing APIs

A Retrofit API is usually written as an interface.

Conceptually,

it looks like this.

```kotlin
interface UserApi {

    @GET("users")
    suspend fun getUsers(): List<UserDto>
}
```

Notice something interesting.

> **Looking back...** The `suspend` keyword here is the same concept from **DOC 2 — Coroutines Core**. Retrofit uses it to make network calls non-blocking — the coroutine suspends while waiting for the HTTP response, freeing the thread for other work.

This is only a description of the API.

There is no implementation.

Yet the application can still call:

```kotlin
userApi.getUsers()
```

How is that possible?

---

## Where Is The Implementation?

Retrofit generates the implementation automatically.

Instead of developers writing networking code for every endpoint,

Retrofit creates the necessary implementation behind the scenes.

As developers,

we only describe what the API looks like.

Retrofit takes responsibility for turning those descriptions into real HTTP requests.

---

## Connecting Everything Together

The networking stack now looks like this.

```text
Application

↓

Retrofit

↓

OkHttp

↓

HTTP

↓

Server
```

Each layer has a different responsibility.

- The application decides **what** it wants.
- Retrofit describes the API.
- OkHttp performs the HTTP communication.
- HTTP defines the communication protocol.

Together,

they provide a clean and maintainable way to communicate with remote servers.

---

## Revision

### Mental Model

```text
Application

↓

Retrofit

↓

OkHttp

↓

HTTP

↓

Server
```

### Remember

Retrofit does not replace OkHttp.

Retrofit describes APIs.

OkHttp performs the HTTP communication.

---

## One Remaining Question

We've learned that Retrofit can transform Kotlin function calls into HTTP requests.

A natural question appears.

When the server responds with JSON,

Retrofit somehow returns objects like:

```kotlin
UserDto

OrderDto

PaymentDto
```

instead of raw JSON.

How does that transformation happen?

That leads us to the next concept.

```text
Converter Factories
```
