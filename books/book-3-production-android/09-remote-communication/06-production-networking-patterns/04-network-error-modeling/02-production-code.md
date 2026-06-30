# Production Code

In the previous document,

we learned that production applications don't expose raw networking exceptions directly.

Instead,

they translate infrastructure-specific failures into application-specific errors.

Let's see how this appears in a real production Android codebase.

---

# What Does It Look Like?

One of the first things you'll notice is that networking exceptions rarely leave the networking layer.

Instead,

you'll often find something similar to this.

```kotlin
sealed interface NetworkError {

    data object NoInternet : NetworkError

    data object RequestTimedOut : NetworkError

    data object AuthenticationRequired : NetworkError

    data object PermissionDenied : NetworkError

    data object ResourceNotFound : NetworkError

    data object TooManyRequests : NetworkError

    data object ServerError : NetworkError

    data object InvalidResponse : NetworkError

    data object Unknown : NetworkError
}
```

Somewhere nearby,

you'll usually find another class responsible for converting exceptions into these application errors.

```kotlin
class ExceptionMapper
```

or

```kotlin
fun Throwable.toNetworkError(): NetworkError
```

At first glance,

this may seem unnecessary.

The application already has exceptions.

Why create another layer?

---

# Why Raw Exceptions Are A Bad API

Imagine exposing networking exceptions throughout the application.

```text
ViewModel

↓

SocketTimeoutException
```

Now the ViewModel needs to understand:

- sockets,
- networking,
- Retrofit,
- OkHttp,
- HTTP libraries.

None of those belong to the presentation layer.

Instead,

production applications expose something much simpler.

```text
ViewModel

↓

NetworkError.RequestTimedOut
```

The ViewModel doesn't care how the failure happened.

It only cares what the application should do next.

This keeps networking details isolated inside the networking layer.

---

# Where Does The Mapping Happen?

A common production architecture looks like this.

```text
Retrofit

↓

Exception

↓

Remote Data Source

↓

Exception Mapper

↓

NetworkError

↓

NetworkResult.Error(...)
```

The networking libraries communicate using exceptions.

The rest of the application communicates using `NetworkError`.

The Exception Mapper translates between the two.

This keeps the networking implementation hidden from the rest of the application.

---

# A Typical Exception Mapper

Although implementations vary,

the overall idea is remarkably consistent.

```kotlin
fun Throwable.toNetworkError(): NetworkError {

    return when (this) {

        is UnknownHostException ->
            NetworkError.NoInternet

        is ConnectException ->
            NetworkError.NoInternet

        is SocketTimeoutException ->
            NetworkError.RequestTimedOut

        is SSLHandshakeException ->
            NetworkError.SecureConnectionFailed

        is HttpException -> {

            when (code()) {

                401 -> NetworkError.AuthenticationRequired

                403 -> NetworkError.PermissionDenied

                404 -> NetworkError.ResourceNotFound

                429 -> NetworkError.TooManyRequests

                in 500..599 -> NetworkError.ServerError

                else -> NetworkError.Unknown
            }
        }

        is SerializationException ->
            NetworkError.InvalidResponse

        is CancellationException ->
            throw this

        else ->
            NetworkError.Unknown
    }
}
```

Don't focus on memorizing this code.

Focus on the responsibility.

The mapper converts infrastructure-specific failures into application-specific errors.

---

# Model Business Meaning, Not Library Details

One of the biggest differences between junior and senior codebases is the language they use.

Avoid exposing library terminology.

Instead of:

```text
SocketTimeoutException
```

prefer:

```text
RequestTimedOut
```

---

Instead of:

```text
Http401
```

prefer:

```text
AuthenticationRequired
```

---

Instead of:

```text
UnknownHostException
```

prefer:

```text
NoInternet
```

Notice the difference.

The application describes what the failure means,

not how the networking library happened to report it.

This makes the application independent of Retrofit,

OkHttp,

or any future networking library.

---

# Common Production Mappings

The following mappings are extremely common in Android applications.

| Networking Exception | Application Error | Typical Action |
|----------------------|-------------------|----------------|
| `UnknownHostException` | `NoInternet` | Ask user to reconnect |
| `ConnectException` | `NoInternet` | Retry after connection |
| `SocketTimeoutException` | `RequestTimedOut` | Offer retry |
| `SSLHandshakeException` | `SecureConnectionFailed` | Inform user / log |
| `HttpException(401)` | `AuthenticationRequired` | Login again |
| `HttpException(403)` | `PermissionDenied` | Show permission error |
| `HttpException(404)` | `ResourceNotFound` | Show not found |
| `HttpException(429)` | `TooManyRequests` | Retry later |
| `HttpException(5xx)` | `ServerError` | Retry |
| `SerializationException` | `InvalidResponse` | Log / report |
| `CancellationException` | *Not an error* | Ignore |

Every project may use different names.

The underlying ideas remain the same.

---

# How This Architecture Evolves

Most applications don't start with a dedicated error model.

The architecture usually evolves over time.

### Stage 1 — Throw Exceptions

```text
Remote Data Source

↓

Throws Exception
```

Simple,

but every caller must understand networking exceptions.

---

### Stage 2 — Catch Everywhere

```text
ViewModel

↓

try / catch

↓

UI
```

Every screen duplicates error handling.

Maintenance becomes difficult.

---

### Stage 3 — Centralize Translation

```text
Exception

↓

Exception Mapper

↓

NetworkError
```

Now every failure is translated consistently.

---

### Stage 4 — Stable Application Contract

```text
NetworkResult

↓

Success

or

NetworkError
```

The rest of the application never depends on networking exceptions again.

---

# Recognizing This In Production

When exploring a production Android project,

you'll commonly encounter patterns such as:

```kotlin
sealed interface NetworkError
```

↓

Application-specific error model.

---

```kotlin
class ExceptionMapper
```

↓

Translates exceptions into application errors.

---

```kotlin
fun Throwable.toNetworkError()
```

↓

Extension-based mapping.

---

```kotlin
NetworkResult.Error(...)
```

↓

Represents a failed network operation.

---

A `when (exception)` expression.

↓

Centralized exception mapping.

---

# Common Beginner Mistakes

### Exposing Exceptions To The UI

```kotlin
NetworkResult.Error(exception)
```

This leaks networking implementation details.

Expose application errors instead.

---

### Showing Exception Messages

```kotlin
exception.message
```

Exception messages are intended for developers,

not users.

The UI should present user-friendly messages based on the application's error model.

---

### Modeling Library Classes

Bad:

```text
SocketTimeoutException
```

Good:

```text
RequestTimedOut
```

Model what the failure means,

not which library reported it.

---

### Catching `Exception` Everywhere

Scattering `try/catch` blocks throughout the application creates inconsistent behavior.

Centralize exception mapping instead.

---

### Treating Cancellation As An Error

A cancelled coroutine usually means the operation is no longer needed.

It isn't a networking failure.

Avoid converting it into a user-visible error.

---

# Production Recognition Checklist

After reading this chapter,

you should immediately recognize the following patterns in a production codebase.

✓ `sealed interface NetworkError`

↓

Application-specific error model.

---

✓ `ExceptionMapper`

↓

Translates infrastructure exceptions into application errors.

---

✓ `Throwable.toNetworkError()`

↓

Centralized exception mapping.

---

✓ `NetworkResult.Error(NetworkError...)`

↓

Stable contract for failed network operations.

---

✓ `when (exception)`

↓

Exception classification.

---

Don't memorize individual exception classes.

Understand the architectural idea.

Production applications keep networking libraries isolated behind their own error model,

allowing the rest of the application to work with a clean,

stable,

and meaningful language.
