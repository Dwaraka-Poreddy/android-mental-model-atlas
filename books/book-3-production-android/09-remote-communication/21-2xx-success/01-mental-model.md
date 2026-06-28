# 2xx Success

## Observation

In the previous chapter,

we learned that HTTP status codes are grouped into categories.

One of those categories is:

```text
2xx
```

A natural question appears.

What does a successful HTTP response actually mean?

Does it always mean:

- data was returned,
- everything worked perfectly,
- or something else?

---

## A Thought Experiment

Imagine you submit a passport application.

After reviewing your application,

the officer says:

> "Your application has been successfully processed."

Notice something important.

That statement doesn't tell you:

- whether you'll receive a passport today,
- whether documents will arrive later,
- or whether additional information is included.

It communicates something more fundamental.

Your request was successfully understood and processed.

HTTP uses the same idea.

---

## What Success Really Means

When a server returns a **2xx** status code,

it is telling the client:

> "I successfully understood your request and processed it."

That's the key idea.

Success is about **processing the request**.

It doesn't necessarily describe what data comes back.

---

## Different Kinds Of Success

Not every successful request looks the same.

For example,

a successful response might:

- return existing information,
- create a new resource,
- complete successfully without returning any data.

These are all successful outcomes.

The specific status code tells the client which kind of success occurred.

We'll study those individual status codes next.

---

## Connecting It To Android

In Retrofit,

you'll often see code like:

```kotlin
if (response.isSuccessful) {
    // Handle success
}
```

Notice that the application isn't checking for:

```text
200
```

It's checking whether the response belongs to the **2xx Success** category.

Only then does it continue processing the response.

---

## The Bigger Picture

A successful HTTP response means the server successfully processed the request.

```text
Client Sends Request
        ↓
Server Processes Request
        ↓
2xx Status Code
        ↓
Client Knows The Request Succeeded
```

The exact kind of success depends on the specific status code.

---

## Production Recognition

Whenever you see:

```kotlin
response.isSuccessful
```

don't think:

> "The server returned data."

Instead think:

> "The server successfully processed my request."

Whether data was returned depends on the specific status code.

---

## Revision

### Mental Model

```text
Request Processed Successfully
            ↓
          2xx
```

### Remember

A **2xx** response means the server successfully understood and processed the request.

The specific status code explains what kind of successful outcome occurred.

---

## One Remaining Question

We've learned what the **2xx Success** category represents.

A natural question appears.

The most common success status code is:

```text
200 OK
```

What does it actually mean,

and when is it returned?

That leads us to the next concept.

```text
200 OK
```
