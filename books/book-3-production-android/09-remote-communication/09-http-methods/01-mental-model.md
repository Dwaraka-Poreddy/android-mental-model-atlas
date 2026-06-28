# HTTP Methods

## Observation

In the previous chapter,

we learned that a client sends a request to a server,

and the server sends back a response.

A natural question appears.

Are all requests the same?

Suppose a client wants to:

- fetch a user's profile,
- create a new account,
- update an address,
- delete a product.

Should every one of these operations look identical?

Probably not.

---

## A Thought Experiment

Imagine you walk into a library.

Sometimes you want to:

- borrow a book,
- return a book,
- reserve a book,
- renew a book.

Although you're interacting with the same librarian,

your intention is different every time.

Simply saying,

> "I'd like something."

isn't enough.

You need to communicate **what kind of operation** you want to perform.

Computers have the same problem.

---

## Different Intentions

A client doesn't always ask for information.

Sometimes it wants to:

- read existing data,
- create new data,
- update existing data,
- remove existing data.

These are fundamentally different operations.

The server needs a way to distinguish between them.

---

## HTTP Methods

HTTP solves this problem using **Methods**.

An **HTTP Method** tells the server what kind of operation the client wants to perform.

Conceptually,

an HTTP request looks like this.

```text
Request
   │
   ├── Method
   └── Other Information
```

The method communicates the client's intention before the server even looks at the rest of the request.

---

## An Example

Imagine two requests.

```text
Request A

Method: GET
```

```text
Request B

Method: DELETE
```

Even if both requests target the same resource,

their intentions are completely different.

The server immediately knows that one client wants to retrieve information,

while the other wants to remove something.

That's the purpose of HTTP methods.

---

## Connecting It To Android

Whenever you write Retrofit code,

you'll often see annotations such as:

```kotlin
@GET(...)

@POST(...)

@PUT(...)

@DELETE(...)
```

These annotations aren't arbitrary.

They tell Retrofit which HTTP method should be used when constructing the request.

The method expresses the client's intention.

---

## The Bigger Picture

An HTTP request doesn't just ask for something.

It also tells the server **what kind of operation** the client wants to perform.

```text
Client Intention
        ↓
HTTP Method
        ↓
Server Knows How To Interpret The Request
```

Without methods,

every request would look the same,

and the server would have no standard way to understand the client's intent.

---

## Production Recognition

Whenever you see:

```kotlin
@GET

@POST

@PUT

@DELETE
```

don't think:

> "These are just Retrofit annotations."

Instead think:

> "These describe the client's intention."

Retrofit simply translates that intention into an HTTP request.

---

## Revision

### Mental Model

```text
Client Intention
        ↓
HTTP Method
        ↓
Server Understands The Request
```

### Remember

HTTP Methods do not identify the resource.

They identify **what the client wants to do** with that resource.

---

## One Remaining Question

We've learned that HTTP methods communicate the client's intention.

A natural question appears.

What are the different HTTP methods,

and when should each one be used?

That leads us to the next concept.

```text
GET
```
