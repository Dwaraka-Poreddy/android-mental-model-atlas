# Production Code

In the previous document,

we learned that Retrofit allows developers to describe APIs instead of manually constructing HTTP requests.

Let's see what that looks like in a real Android application.

---

## Where Does Retrofit Come From?

A natural question appears.

Is Retrofit part of Android itself?

No.

Retrofit is a third-party open-source library developed by Square.

Applications add Retrofit as a project dependency,

just like they add OkHttp.

Retrofit doesn't perform HTTP communication by itself.

Instead,

it builds on top of an HTTP client such as OkHttp,

providing a higher-level way to describe and organize APIs.

This is why Retrofit and OkHttp are almost always used together in Android applications.

---

## A Typical Retrofit Instance

One of the first things you'll encounter in a production codebase is a Retrofit instance.

```kotlin
val retrofit = Retrofit.Builder()
    .baseUrl(BASE_URL)
    .client(okHttpClient)
    .build()
```

Notice something interesting.

Retrofit isn't created by itself.

It receives an existing `OkHttpClient`.

This reinforces an important idea:

```text
Retrofit

↓

Uses

↓

OkHttp
```

Retrofit builds on top of OkHttp.

It does not replace it.

---

## Creating An API

Once Retrofit has been created,

applications usually define APIs as Kotlin interfaces.

For example,

```kotlin
interface UserApi {

    @GET("users")
    suspend fun getUsers(): List<UserDto>

    @POST("users")
    suspend fun createUser(
        @Body request: CreateUserRequest
    ): UserDto
}
```

This doesn't look like networking code.

It looks like an ordinary Kotlin interface.

That's exactly the goal.

Instead of manually constructing HTTP requests,

developers simply describe the available APIs.

---

## Where Is The Implementation?

This is one of the most common questions beginners ask.

The interface contains function declarations,

but no implementation.

```kotlin
interface UserApi {

    @GET("users")
    suspend fun getUsers(): List<UserDto>
}
```

Yet the following code works.

```kotlin
val users = userApi.getUsers()
```

So where is `getUsers()` actually implemented?

The answer is:

Retrofit generates the implementation automatically.

When the application starts,

Retrofit reads the interface,

understands the annotations,

and creates an object capable of making the corresponding HTTP requests.

As developers,

we never write that implementation ourselves.

---

## Why Use Interfaces?

At first,

using an interface instead of a normal class might seem unusual.

The reason is simple.

Interfaces allow developers to describe **what** an API looks like,

without worrying about **how** each HTTP request is performed.

Retrofit takes care of the implementation,

allowing the application to focus on business logic instead of networking boilerplate.

---

## What Are These Annotations?

You'll frequently encounter annotations such as:

```kotlin
@GET(...)

@POST(...)

@PUT(...)

@PATCH(...)

@DELETE(...)
```

These annotations describe the HTTP method associated with each function.

Notice how they directly correspond to the HTTP methods we learned earlier.

```text
HTTP

↓

GET

POST

PUT

PATCH

DELETE

↓

Retrofit Annotations
```

Retrofit is simply providing a Kotlin-friendly way of describing HTTP requests.

---

## Where Is Retrofit Usually Created?

Production Android applications typically create Retrofit only once.

Conceptually,

the setup looks like this.

```text
Create OkHttpClient

↓

Create Retrofit

↓

Create API Interfaces

↓

Reuse Throughout The Application
```

Rather than creating new Retrofit instances for every request,

the same configured instance is shared across the application.

---

## Recognizing Retrofit

When exploring an unfamiliar Android project,

you'll often recognize Retrofit by patterns such as:

```kotlin
Retrofit.Builder()
```

```kotlin
interface UserApi
```

```kotlin
@GET(...)
```

```kotlin
@POST(...)
```

```kotlin
retrofit.create(UserApi::class.java)
```

Whenever you see these,

you're almost certainly looking at Retrofit.

---

## Common Beginner Misconceptions

### "Retrofit sends HTTP requests."

Not directly.

Retrofit delegates the actual HTTP communication to OkHttp.

---

### "The API interface is the implementation."

No.

It is only a description.

Retrofit generates the implementation automatically.

---

### "Retrofit replaces OkHttp."

No.

Retrofit depends on OkHttp.

Without an HTTP client,

Retrofit has nothing to execute requests with.

---

### "Annotations are just decoration."

No.

Annotations provide the information Retrofit uses to understand:

- which HTTP method to use,
- which endpoint to call,
- how parameters should be sent,
- and how requests should be constructed.

Without them,

Retrofit wouldn't know how to build the HTTP request.

---

## Production Recognition Checklist

After reading this chapter,

you should immediately recognize the following patterns in a production codebase.

```kotlin
Retrofit.Builder()
```

↓

A Retrofit instance is being configured.

---

```kotlin
interface SomethingApi
```

↓

An API is being described.

---

```kotlin
@GET(...)
@POST(...)
@PUT(...)
```

↓

Retrofit annotations describing HTTP requests.

---

```kotlin
retrofit.create(...)
```

↓

Retrofit is generating an implementation for the API interface.

---

You don't need to understand every annotation yet.

The important thing is recognizing that Retrofit allows developers to describe APIs,

while OkHttp performs the underlying HTTP communication.
