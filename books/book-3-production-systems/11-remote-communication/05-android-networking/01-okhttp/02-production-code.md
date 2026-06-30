# Production Code

In the previous document,

we learned why OkHttp exists.

Now let's see what it looks like in a real Android application.

---

## Where Does OkHttp Come From?

One question naturally appears.

Is OkHttp part of Android itself?

No.

OkHttp is an external open-source library developed by Square.

Applications add it as a project dependency,

just like many other Android libraries.

Once included,

your application can create and use `OkHttpClient` objects to perform HTTP communication.

Today,

most Android applications don't interact with OkHttp directly.

Instead,

they usually use Retrofit,

which internally relies on OkHttp to perform network requests.

We'll understand that relationship in the next chapter.

---

## A Typical OkHttp Client

One of the first things you'll encounter in a production codebase is an `OkHttpClient`.

```kotlin
val client = OkHttpClient.Builder()
    .build()
```

Although this looks simple,

it represents the application's HTTP client.

Instead of creating a new client for every request,

production applications usually create one shared instance and reuse it throughout the application.

---

## Why Only One OkHttpClient?

A natural question appears.

Why not simply create a new `OkHttpClient()` every time we need to make a request?

```kotlin
fun fetchUser() {
    val client = OkHttpClient()

    // Make request...
}
```

Technically,

this works.

However,

it throws away many of the optimizations that OkHttp provides.

A shared client can reuse resources such as:

- existing connections,
- thread pools,
- internal caches,
- and other networking infrastructure.

Creating a new client for every request means rebuilding all of that work repeatedly.

For this reason,

production applications almost always reuse a single `OkHttpClient` instance.

---

## The Builder Pattern

You'll rarely see code like this.

```kotlin
val client = OkHttpClient()
```

Instead,

you'll usually find:

```kotlin
val client = OkHttpClient.Builder()
    .build()
```

This is known as the **Builder Pattern**.

It allows the application to configure the client before creating it.

For example,

later you'll see configuration such as:

```kotlin
OkHttpClient.Builder()
    .connectTimeout(...)
    .readTimeout(...)
    .addInterceptor(...)
    .build()
```

The builder makes it easy to customize the client while keeping object creation clean and readable.

We'll explore each of these configurations in the upcoming chapters.

---

## Where Is It Usually Created?

In production applications,

the `OkHttpClient` is usually created only once during application startup.

After that,

the same instance is shared wherever networking is needed.

Conceptually,

it looks like this.

```text
Application Starts

↓

Create OkHttpClient

↓

Reuse Everywhere
```

The exact location depends on the application's architecture,

but the idea remains the same:

one client,

many requests.

---

## Recognizing OkHttp

When exploring an unfamiliar Android project,

you'll often recognize OkHttp by code such as:

```kotlin
OkHttpClient.Builder()

.newCall(...)

.execute()

.enqueue(...)
```

Even if another library is used for networking,

there's a good chance OkHttp is still working underneath.

Learning to recognize these patterns makes production code much easier to understand.

---

## Common Beginner Misconceptions

### "OkHttp replaces HTTP."

No.

HTTP is the communication protocol.

OkHttp is a library that implements HTTP communication.

---

### "Retrofit replaces OkHttp."

No.

Retrofit is built on top of OkHttp.

When Retrofit performs a network request,

OkHttp is usually the component that actually sends it.

---

### "Creating a new OkHttpClient for every request is fine."

While it works,

it is generally inefficient.

Production applications almost always reuse a single client instance.

---

## Production Recognition Checklist

After reading this chapter,

you should immediately recognize the following patterns in a production codebase.

```kotlin
OkHttpClient.Builder()
```

↓

An HTTP client is being configured.

---

```kotlin
.newCall(request)
```

↓

An HTTP request is about to be executed.

---

```kotlin
.execute()
```

↓

A synchronous HTTP request.

---

```kotlin
.enqueue(...)
```

↓

An asynchronous HTTP request.

---

You don't need to understand every API yet.

The important thing is recognizing that these patterns belong to OkHttp and are responsible for performing HTTP communication.
