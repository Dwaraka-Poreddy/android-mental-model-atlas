# Body

## Observation

In the previous chapter,

we learned that headers provide additional information about an HTTP request.

For example,

they can tell the server:

- who is making the request,
- which language the client prefers,
- what type of response it expects.

A natural question appears.

Headers describe the request,

but what if the client needs to send the actual data itself?

For example:

- a registration form,
- login credentials,
- a new product,
- a chat message.

Where does that information go?

---

## A Thought Experiment

Imagine you're sending a package to a friend.

The package has a shipping label.

The label contains information like:

- the sender,
- the recipient,
- the delivery priority.

But the label isn't the reason you're sending the package.

The actual reason is what's inside the box.

The contents of the package are what really matter.

HTTP requests follow the same idea.

Headers describe the request.

The body contains the actual data being sent.

---

## Body

HTTP solves this using the **Request Body**.

The **Body** is the part of an HTTP request that carries the actual data from the client to the server.

Unlike headers,

which describe the request,

the body contains the information the server is expected to process.

---

## Some Examples

A registration request might send:

```text
Name: Alice
Email: alice@example.com
Password: ********
```

A messaging application might send:

```text
Message:
"Hello!"
```

A shopping application might send:

```text
Product ID: 123
Quantity: 2
```

In every case,

the body contains the actual information the client wants the server to receive.

---

## Body vs Headers

This is another important distinction.

Headers answer questions like:

- Who is making the request?
- What language is preferred?
- What format is being used?

The body answers a different question.

> **What data is the client sending?**

Conceptually,

the difference looks like this.

```text
Headers
      ↓
Information About The Request

------------------------

Body
      ↓
The Actual Data
```

---

## Connecting It To Android

In Retrofit,

the request body is represented using the `@Body` annotation.

```kotlin
@POST("/users")
suspend fun createUser(
    @Body request: CreateUserRequest
): User
```

When the request is sent,

Retrofit converts the `CreateUserRequest` object into a format the server understands,

and places it inside the request body.

---

## The Bigger Picture

Every part of an HTTP request has a different responsibility.

```text
Method
      ↓
What Operation?

------------------------

URL
      ↓
Which Resource?

------------------------

Headers
      ↓
Additional Context

------------------------

Body
      ↓
Actual Data
```

Together,

these pieces completely describe the client's request.

---

## Production Recognition

Whenever you see:

```kotlin
@Body

CreateUserRequest

LoginRequest

UpdateProfileRequest
```

don't think:

> "This is just another Kotlin object."

Instead think:

> "This object becomes the body of the HTTP request."

It contains the actual data being sent to the server.

---

## Revision

### Mental Model

```text
Headers
      ↓
Information About The Request

------------------------

Body
      ↓
Actual Data
```

### Remember

Headers describe the request.

The body contains the data.

They serve different purposes.

---

## One Remaining Question

We've now learned every major part of an HTTP request.

A natural question appears.

Once the server processes the request,

how does it tell the client whether the operation succeeded,

failed,

or encountered an error?

That leads us to the next concept.

```text
HTTP Status Codes
```
