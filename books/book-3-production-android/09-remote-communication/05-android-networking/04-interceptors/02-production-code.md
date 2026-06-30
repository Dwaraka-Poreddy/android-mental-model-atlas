# Production Code

In the previous document,

we learned that Interceptors allow applications to apply common behavior to every HTTP request and response.

Let's see what this looks like in a real Android application.

---

## Where Does It Come From?

A natural question appears.

Are Interceptors part of Android itself?

No.

Interceptors are a feature provided by the OkHttp library.

Since Retrofit uses OkHttp internally,

applications configured with Retrofit can also take advantage of OkHttp Interceptors.

---

## How Does Retrofit Use Interceptors?

A natural question appears.

Most Android applications use Retrofit.

So where are Interceptors actually added?

Interceptors are **not** added to Retrofit.

Instead,

they are added to the `OkHttpClient` that Retrofit uses internally.

A typical setup looks like this.

```kotlin
val okHttpClient = OkHttpClient.Builder()
    .addInterceptor(AuthInterceptor())
    .addInterceptor(LoggingInterceptor())
    .build()

val retrofit = Retrofit.Builder()
    .baseUrl(BASE_URL)
    .client(okHttpClient)
    .addConverterFactory(
        GsonConverterFactory.create()
    )
    .build()
```

Notice how each component has its own responsibility.

- Retrofit describes APIs.
- Converter Factories translate JSON.
- OkHttp performs HTTP communication.
- Interceptors customize that HTTP communication.

Retrofit doesn't provide its own interceptor mechanism.

It simply uses the configured `OkHttpClient`.

---

## Registering Multiple Interceptors

Production applications rarely have only one interceptor.

Instead,

multiple interceptors are registered with the `OkHttpClient`.

```kotlin
val client = OkHttpClient.Builder()
    .addInterceptor(AuthInterceptor())
    .addInterceptor(LoggingInterceptor())
    .build()
```

Each interceptor focuses on one responsibility.

For example,

- one may add authentication tokens,
- another may log requests and responses,
- another may add common headers.

Keeping each interceptor focused on a single responsibility makes the networking pipeline easier to understand and maintain.

---

## A Typical Interceptor

A simplified authentication interceptor might look like this.

```kotlin
class AuthInterceptor(
    private val tokenProvider: TokenProvider
) : Interceptor {

    override fun intercept(
        chain: Interceptor.Chain
    ): Response {

        val request = chain.request()

        val authenticatedRequest =
            request.newBuilder()
                .addHeader(
                    "Authorization",
                    "Bearer ${tokenProvider.token}"
                )
                .build()

        return chain.proceed(authenticatedRequest)
    }
}
```

Although this may seem unfamiliar at first,

every line has a specific responsibility.

---

## Breaking It Down

The interceptor first retrieves the current request.

```kotlin
val request = chain.request()
```

This represents the HTTP request that is about to be sent.

---

To modify the request,

a new builder is created.

```kotlin
request.newBuilder()
```

The builder allows headers and other request properties to be added or modified before the request is sent.

---

Finally,

the request continues through the networking pipeline.

```kotlin
return chain.proceed(authenticatedRequest)
```

This is one of the most important lines in an interceptor.

Calling `proceed()` tells OkHttp:

> "Continue processing this request."

Without calling `proceed()`,

the request never reaches the server.

---

## What Happens When An API Is Called?

Suppose the application executes:

```kotlin
userApi.getUsers()
```

Conceptually,

the request follows this path.

```text
userApi.getUsers()

↓

Retrofit

↓

OkHttp

↓

Authentication Interceptor

↓

Logging Interceptor

↓

HTTP Request

↓

Server

↓

HTTP Response

↓

Logging Interceptor

↓

Authentication Interceptor

↓

Retrofit

↓

Converter Factory

↓

List<UserDto>
```

Notice how each layer performs one specific responsibility.

No single library performs the entire networking process.

Instead,

Retrofit,

OkHttp,

Converter Factories,

and Interceptors each contribute one part of the networking pipeline.

---

## Common Production Interceptors

Although every application is different,

you'll frequently encounter interceptors responsible for:

- Authentication
- Logging
- Common Headers
- Analytics
- Request Timing

Each interceptor has a single responsibility,

making the networking pipeline easier to understand and maintain.

---

## Recognizing Interceptors

When exploring an unfamiliar Android project,

you'll often recognize patterns such as:

```kotlin
.addInterceptor(...)
```

Registers an interceptor with the `OkHttpClient`.

You'll usually find this while configuring the `OkHttpClient` that is later supplied to Retrofit.

---

```kotlin
class SomethingInterceptor : Interceptor
```

Defines a custom interceptor.

---

```kotlin
chain.request()
```

Retrieves the current HTTP request.

---

```kotlin
request.newBuilder()
```

Creates a modified version of the request.

---

```kotlin
chain.proceed(...)
```

Continues the request through the networking pipeline.

---

## Common Beginner Misconceptions

### "Interceptors belong to Retrofit."

No.

Interceptors belong to OkHttp.

Retrofit simply uses the configured `OkHttpClient`.

---

### "Interceptors only modify requests."

No.

They can inspect and modify both requests and responses.

---

### "Calling `proceed()` is optional."

No.

Without calling `chain.proceed(...)`,

the request does not continue to the server.

---

### "Applications usually have only one interceptor."

No.

Production applications commonly register multiple interceptors,

each responsible for one concern.

---

## Production Recognition Checklist

After reading this chapter,

you should immediately recognize the following patterns.

```kotlin
.addInterceptor(...)
```

↓

Registers behavior that runs for every HTTP request and response.

---

```kotlin
class AuthInterceptor : Interceptor
```

↓

Defines an interceptor.

---

```kotlin
chain.request()
```

↓

Retrieves the current request.

---

```kotlin
request.newBuilder()
```

↓

Creates a modified request.

---

```kotlin
chain.proceed(...)
```

↓

Passes the request to the next interceptor or ultimately to the network.

---

The exact responsibility of an interceptor may differ from one project to another.

The important thing to recognize is that interceptors provide a central place for applying common networking behavior to every HTTP request and response.
