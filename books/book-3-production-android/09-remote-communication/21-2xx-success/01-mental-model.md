# 2xx Success

## Observation

In the previous chapter,

we learned that every HTTP status code belongs to a category.

One of those categories is:

```text
2xx
```

A natural question appears.

What does a successful HTTP response actually mean?

Does it always mean:

- data was returned,
- something was created,
- or something else?

---

## A Thought Experiment

Imagine you submit a passport application.

After reviewing it,

the officer says:

> "Your application has been successfully processed."

Notice what that statement doesn't tell you.

It doesn't tell you:

- whether you'll receive your passport immediately,
- whether it will arrive by mail,
- or whether there's anything else to collect.

It communicates something more fundamental.

Your request was successfully understood and processed.

HTTP uses the same idea.

---

## What Success Really Means

Whenever a server returns a **2xx** status code,

it is telling the client:

> "I successfully understood your request and processed it."

That is the mental model.

Everything else is simply a more specific description of **how** the request succeeded.

---

## Common Success Responses

Although every **2xx** response represents success,

different status codes communicate different successful outcomes.

### 200 OK

```text
Request Processed Successfully
        +
Requested Data Returned
```

This is the most common success response.

It is typically returned when the server successfully processes a request and returns the requested information.

Examples include:

- retrieving a user profile,
- fetching products,
- loading today's weather.

---

### 201 Created

```text
Request Processed Successfully
        +
New Resource Created
```

This response indicates that the server successfully created something new.

Examples include:

- creating a new account,
- placing an order,
- publishing a post.

---

### 204 No Content

```text
Request Processed Successfully
        +
Nothing To Return
```

Sometimes the server successfully completes the request,

but there is no data that needs to be returned.

For example,

after successfully deleting a resource,

the server may simply indicate that the operation completed.

---

## Connecting It To Android

In Retrofit,

you'll often see code like:

```kotlin
if (response.isSuccessful) {
    ...
}
```

Notice that the application isn't checking specifically for:

- 200,
- 201,
- or 204.

Instead,

it's asking a more important question.

> "Did the server successfully process my request?"

Only after that does the application decide how to handle the response.

---

## The Bigger Picture

Every **2xx** status code communicates the same core idea.

```text
Request Processed Successfully
            ↓
          2xx
```

The specific status code simply explains what kind of successful outcome occurred.

---

## Production Recognition

When reading production code,

don't try to memorize every success status code individually.

Instead,

reason like this.

```text
2xx
    ↓
Success

↓

Which kind of success?
```

Then look at the specific status code if you need more detail.

---

## Revision

### Mental Model

```text
Request Processed Successfully
            ↓
          2xx
```

### Common Success Responses

```text
200
    ↓
Success
+
Returned Data

------------------------

201
    ↓
Success
+
Created Something

------------------------

204
    ↓
Success
+
Nothing To Return
```

### Remember

All **2xx** responses mean the server successfully processed the request.

The specific status code simply provides more detail about the successful outcome.

---

## One Remaining Question

We've learned what successful responses look like.

A natural question appears.

What happens when the server cannot process the client's request because something is wrong with the request itself?

That leads us to the next concept.

```text
4xx Client Errors
```
