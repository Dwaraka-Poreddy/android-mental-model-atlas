# Production Code

In the previous document,

we learned that a Remote Data Source provides a single place responsible for all remote communication.

Let's see how this appears in a real production Android application.

---

## What Does It Look Like?

One of the simplest Remote Data Sources you might encounter looks like this.

```kotlin
class UserRemoteDataSource(
    private val userApi: UserApi
) {

    suspend fun getProfile(): UserDto {
        return userApi.getProfile()
    }

    suspend fun updateProfile(
        request: UpdateProfileRequestDto
    ) {
        userApi.updateProfile(request)
    }
}
```

At first glance,

many developers have the same reaction.

> "This class simply forwards API calls.
>
> Why does it even exist?"

It's a perfectly reasonable question.

---

## Why Introduce Another Layer?

For a very small application,

this layer may indeed look unnecessary.

The `RemoteDataSource` often starts as a thin wrapper around the Retrofit service.

```text
Application

↓

UserRemoteDataSource

↓

UserApi
```

If that's all it ever did,

the layer wouldn't provide much value.

However,

production applications rarely stay that simple.

As applications grow,

this layer gradually becomes the single owner of all remote communication.

---

## How It Evolves

The real value of a Remote Data Source isn't what it does on day one.

It's what it becomes as the application grows.

---

### Stage 1 — Forwarding API Calls

Initially,

it may simply delegate to the Retrofit service.

```kotlin
suspend fun getProfile(): UserDto {
    return userApi.getProfile()
}
```

This keeps the rest of the application independent of the networking library.

---

### Stage 2 — Coordinating Multiple APIs

Later,

one operation may require data from multiple backend endpoints.

```kotlin
suspend fun getUserDashboard(): DashboardDto {

    val profile = userApi.getProfile()

    val preferences = userApi.getPreferences()

    return DashboardDto(
        profile = profile,
        preferences = preferences
    )
}
```

The caller doesn't need to know that two different API calls were required.

The Remote Data Source coordinates them.

---

### Stage 3 — Preparing Requests

Sometimes,

requests need additional preparation before being sent.

For example,

building request objects,

adding optional fields,

or transforming input into the format expected by the backend.

```kotlin
val request = UpdateProfileRequestDto(
    name = name.trim(),
    email = email.lowercase()
)

userApi.updateProfile(request)
```

This preparation remains centralized inside the Remote Data Source.

---

### Stage 4 — Preparing For Error Handling

As applications mature,

all remote failures begin flowing through one place.

```text
Network Request

↓

Remote Data Source

↓

Failure Detected

↓

Future:
Map To Application Error
```

We'll learn how production applications model these failures in the upcoming chapters.

The important idea for now is that there is a single place where remote failures naturally arrive.

---

### Stage 5 — Supporting Future Features

Over time,

the Remote Data Source may also become responsible for operations such as:

- refreshing authentication tokens,
- retrying requests,
- pagination,
- request batching,
- feature-specific networking logic.

Not every application needs all of these.

The important point is that there is now a single layer where these responsibilities naturally belong.

---

## Why This Design Scales

Imagine that every screen communicated directly with `UserApi`.

Eventually,

every screen would need to know:

- which API to call,
- how many API calls are required,
- how requests should be prepared,
- how remote failures should be handled.

That knowledge would become scattered throughout the application.

Instead,

all remote communication stays in one place.

```text
Application

↓

Remote Data Source

↓

API Service Layer

↓

Server
```

The rest of the application only asks for data.

It doesn't need to know how that data was obtained.

---

## Recognizing Remote Data Sources

When exploring a production codebase,

you'll often recognize Remote Data Sources by patterns such as:

```kotlin
class UserRemoteDataSource
```

↓

Owns communication with remote services related to users.

---

```kotlin
private val userApi: UserApi
```

↓

Uses one or more Retrofit API services internally.

---

```kotlin
suspend fun
```

↓

Exposes operations that retrieve or update remote data.

---

Multiple API calls inside one function.

↓

Coordinates several backend endpoints into one application operation.

---

## Common Beginner Misconceptions

### "This layer only forwards API calls."

Initially,

it often does.

That simplicity is intentional.

As the application grows,

this layer naturally becomes the home for increasingly complex remote communication.

---

### "Every screen can call Retrofit directly."

Technically,

yes.

But doing so spreads networking knowledge throughout the application,

making it harder to maintain.

The Remote Data Source keeps remote communication centralized.

---

### "Remote Data Source replaces the Repository."

No.

The Repository is a higher-level architectural concept.

It coordinates different sources of data,

such as remote services,

local databases,

and caches.

We'll explore the Repository Pattern in the Android Architecture book.

---

## Production Recognition Checklist

After reading this chapter,

you should immediately recognize the following patterns.

```kotlin
class SomethingRemoteDataSource
```

↓

Owns remote communication for a particular feature.

---

```kotlin
private val someApi: SomeApi
```

↓

Depends on one or more Retrofit API services.

---

```kotlin
suspend fun
```

↓

Exposes remote operations to the rest of the application.

---

Multiple API calls inside one function.

↓

Coordinates several backend endpoints into one application-level operation.

---

The first version of a Remote Data Source may appear simple.

Don't judge it by what it does today.

Judge it by the responsibility it owns.

As production applications evolve,

this layer becomes the central place for remote communication,

keeping networking concerns organized and maintainable.
