# URL

## Observation

In the previous chapters,

we learned that HTTP methods communicate the client's intention.

For example,

- GET retrieves information.
- POST creates information.
- DELETE removes information.

A natural question appears.

Knowing the operation isn't enough.

Suppose the client wants to retrieve:

- a user,
- a product,
- an order.

How does the server know **which** resource the client is referring to?

---

## A Thought Experiment

Imagine visiting a large library.

You walk up to the librarian and simply say:

> "I'd like to borrow a book."

The librarian's first question would probably be:

> "Which book?"

Your request is incomplete.

Knowing **what** you want to do isn't enough.

The librarian also needs to know **what you're doing it to**.

Computers have exactly the same problem.

---

## Identifying The Resource

Every HTTP request performs an operation on something.

That "something" is called a **resource**.

Examples of resources include:

- a user,
- a product,
- a shopping cart,
- an order,
- a message.

Before the server can process the request,

the client must identify the resource it is talking about.

---

## URL

HTTP solves this using a **URL**.

A URL identifies the resource that the client wants to access.

Conceptually,

an HTTP request contains two essential pieces of information.

```text
HTTP Method
        +
URL
        ↓
Complete Request
```

The method answers:

> "What do you want to do?"

The URL answers:

> "What do you want to do it to?"

Both are required.

---

## An Example

Consider these requests.

```text
GET /users
```

```text
DELETE /users/123
```

The HTTP method changes the operation.

The URL identifies the resource.

Together,

they completely describe the client's intention.

---

## Connecting It To Android

Whenever you write Retrofit code like:

```kotlin
@GET("/users")

@POST("/orders")

@DELETE("/users/{id}")
```

the annotation contains more than just the HTTP method.

It also specifies the URL that identifies the resource the request targets.

Retrofit combines the method and the URL to construct the HTTP request.

---

## The Bigger Picture

An HTTP request answers two fundamental questions.

```text
What?
        ↓
HTTP Method

------------------------

Which Resource?
        ↓
URL
```

Neither one is sufficient on its own.

The server needs both.

---

## Production Recognition

Whenever you see:

```kotlin
@GET("/users")

@POST("/orders")

@PATCH("/profile")
```

don't think:

> "That's just a string."

Instead think:

> "This URL identifies the resource my application wants to interact with."

The HTTP method and the URL always work together.

---

## Revision

### Mental Model

```text
HTTP Method
        +
URL
        ↓
Complete Request
```

### Remember

The HTTP method describes the operation.

The URL identifies the resource.

Together,

they describe what the client wants the server to do.

---

## One Remaining Question

We've learned that the URL identifies the resource.

A natural question appears.

Sometimes we write:

```text
/users
```

Other times we write:

```text
/users/123
```

What does that extra value represent?

That leads us to the next concept.

```text
Path Parameters
```
