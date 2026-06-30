# Production Code

In the previous document,

we learned what timeouts are and why they exist.

Let's now see how they're configured and used in production Android applications.

---

## Where Are Timeouts Configured?

Timeouts are configured while creating the application's shared `OkHttpClient`.

A typical setup looks like this.

```kotlin
val okHttpClient = OkHttpClient.Builder()
    .connectTimeout(10, TimeUnit.SECONDS)
    .readTimeout(30, TimeUnit.SECONDS)
    .writeTimeout(30, TimeUnit.SECONDS)
    .build()
```

The configured client is then supplied to Retrofit.

```kotlin
val retrofit = Retrofit.Builder()
    .baseUrl(BASE_URL)
    .client(okHttpClient)
    .addConverterFactory(
        GsonConverterFactory.create()
    )
    .build()
```

Every request performed through this Retrofit instance automatically uses these timeout values.

---

## Breaking Down The Configuration

When reading production code,

you'll commonly encounter:

```kotlin
.connectTimeout(...)
```

Configures how long OkHttp waits while establishing a connection.

---

```kotlin
.readTimeout(...)
```

Configures how long OkHttp waits while receiving data.

---

```kotlin
.writeTimeout(...)
```

Configures how long OkHttp waits while sending data.

---

## Why Are The Values Different?

Production applications often configure different values.

```kotlin
.connectTimeout(10, TimeUnit.SECONDS)
.readTimeout(30, TimeUnit.SECONDS)
.writeTimeout(30, TimeUnit.SECONDS)
```

This doesn't mean one timeout is more important than another.

Each stage simply has different expectations.

Connecting to a server is usually quick.

Uploading or downloading data may legitimately take longer.

For this reason,

different stages often use different timeout values.

---

## Do All APIs Use The Same Timeouts?

Not always.

Most applications use one shared `OkHttpClient`.

```text
Profile API

↓

Shared OkHttpClient

↓

10s / 30s / 30s
```

This works well for the majority of APIs.

However,

some operations naturally require different timeout values.

For example:

- uploading large videos,
- downloading large files,
- generating reports,
- importing or exporting data.

These operations may take significantly longer than a typical API request.

---

## Different Clients For Different Use Cases

A common production approach is to create multiple `OkHttpClient` instances.

For example,

```kotlin
val defaultClient = OkHttpClient.Builder()
    .connectTimeout(10, TimeUnit.SECONDS)
    .readTimeout(30, TimeUnit.SECONDS)
    .writeTimeout(30, TimeUnit.SECONDS)
    .build()

val uploadClient = defaultClient
    .newBuilder()
    .writeTimeout(2, TimeUnit.MINUTES)
    .build()

val reportClient = defaultClient
    .newBuilder()
    .readTimeout(2, TimeUnit.MINUTES)
    .build()
```

Notice something important.

The new clients reuse the existing configuration.

Only the timeout that needs to change is overridden.

This avoids duplicating the entire networking configuration.

---

## When Would You Do This?

Different timeout configurations are common for operations such as:

| Operation | Common Reason |
|-----------|---------------|
| User profile | Small, fast request |
| Uploading videos | Large request body |
| Downloading files | Large response body |
| Exporting reports | Server-side processing may take longer |

The exact values differ from one application to another.

The important idea is that timeout configuration should match the nature of the operation.

---

## Recognizing Timeouts

When exploring a production codebase,

you'll often recognize timeout configuration by patterns such as:

```kotlin
.connectTimeout(...)
```

↓

Connection timeout.

---

```kotlin
.readTimeout(...)
```

↓

Response timeout.

---

```kotlin
.writeTimeout(...)
```

↓

Request timeout.

---

```kotlin
.newBuilder()
```

↓

Reuse an existing `OkHttpClient` while changing only a few settings.

This is a common production pattern for creating specialized clients.

---

## Common Beginner Mistakes

### Configuring timeouts on Retrofit

```kotlin
Retrofit.Builder()
```

Retrofit does not provide timeout configuration.

Timeouts belong to the underlying `OkHttpClient`.

---

### Using one timeout value everywhere

Different operations have different characteristics.

Large uploads and report generation often require different timeout values than ordinary API requests.

---

### Creating completely new clients

Instead of writing:

```kotlin
OkHttpClient.Builder()
```

again,

production applications often start from:

```kotlin
defaultClient.newBuilder()
```

This preserves the existing configuration,

such as interceptors,

connection settings,

and other shared networking behavior.

---

## Production Recognition Checklist

After reading this chapter,

you should immediately recognize the following patterns.

```kotlin
.connectTimeout(...)
```

↓

Configure connection timeout.

---

```kotlin
.readTimeout(...)
```

↓

Configure read timeout.

---

```kotlin
.writeTimeout(...)
```

↓

Configure write timeout.

---

```kotlin
defaultClient.newBuilder()
```

↓

Create a specialized client by reusing an existing configuration.

---

The exact timeout values may vary across projects.

The important thing is recognizing that production applications often maintain one default client while creating specialized clients for operations with different networking requirements.
