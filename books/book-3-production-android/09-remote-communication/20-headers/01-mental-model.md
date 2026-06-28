# Headers

## Observation

In the previous chapters,

we learned that an HTTP request contains:

- an HTTP method,
- a URL,
- and optionally query parameters.

Together,

they tell the server:

- what operation the client wants to perform,
- which resource it wants,
- and how the results should be customized.

A natural question appears.

Sometimes,

the client also needs to send information such as:

- who is making the request,
- what language the user prefers,
- what format the response should use,
- which version of the application is making the request.

None of this identifies the resource.

So where should that information go?

---

## A Thought Experiment

Imagine you're sending a parcel.

The parcel contains the actual item you're sending.

But before the courier can deliver it,

they also need information like:

- the sender,
- the recipient,
- whether it's fragile,
- whether it requires a signature.

Notice something important.

None of this is the parcel itself.

It's information **about** the parcel.

HTTP requests have the same requirement.

Sometimes,

the client needs to send information **about the request itself**.

---

## Headers

HTTP solves this using **Headers**.

A **Header** is metadata that provides additional information about an HTTP request or response.

Headers don't identify the resource.

They don't describe the operation.

Instead,

they provide context that helps the client and server communicate correctly.

---

## Some Examples

A request might include headers such as:

```text
Authorization: Bearer <token>
```

to identify the current user.

---

```text
Accept: application/json
```

to tell the server which response format the client expects.

---

```text
Accept-Language: en-US
```

to indicate the user's preferred language.

Each header communicates additional information,

without changing the requested resource.

---

## Connecting It To Android

In Retrofit,

headers are commonly added using the `@Header` annotation.

```kotlin
@GET("/profile")
suspend fun getProfile(
    @Header("Authorization") token: String
): User
```

When the request is sent,

Retrofit includes the header alongside the rest of the request.

The server can then use that information while processing the request.

---

## The Bigger Picture

Every part of an HTTP request has a different responsibility.

```text
Method
      ↓
What operation?

------------------------

URL
      ↓
Which resource?

------------------------

Headers
      ↓
Additional Context
```

Headers don't replace the URL.

They complement it.

---

## Production Recognition

Whenever you see:

```kotlin
@Header("Authorization")

@Header("Accept-Language")

@Header("User-Agent")
```

don't think:

> "These are just extra parameters."

Instead think:

> "These provide additional context that helps the server process the request."

The resource hasn't changed.

The request has become more informative.

---

## Revision

### Mental Model

```text
Method
      ↓
What?

------------------------

URL
      ↓
Which Resource?

------------------------

Headers
      ↓
Additional Context
```

### Remember

Headers contain metadata about the request or response.

They provide context,

not the resource itself.

---

## One Remaining Question

We've learned how a client can provide additional context using headers.

A natural question appears.

What if the client needs to send the actual data itself?

For example:

- a registration form,
- a login request,
- a new product,
- a chat message.

Where does that data go?

That leads us to the next concept.

```text
Body
```
