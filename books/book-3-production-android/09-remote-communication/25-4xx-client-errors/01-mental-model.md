# 4xx Client Errors

## Observation

In the previous chapter,

we learned that every **2xx** status code represents a successful request.

A natural question appears.

What happens when the server cannot process the request,

not because the server is broken,

but because something is wrong with the request itself?

For example,

- the request is malformed,
- the user isn't logged in,
- the user doesn't have permission,
- or the requested resource doesn't exist.

How does the server communicate these situations?

---

## A Thought Experiment

Imagine you're visiting a library.

You ask the librarian for a book.

Several things could go wrong.

Perhaps:

- you filled out the request form incorrectly,
- you forgot your library membership card,
- you're trying to access a restricted archive,
- or the book simply doesn't exist.

Notice something important.

In every case,

the librarian is perfectly capable of helping you.

The problem isn't with the library.

The problem is with the request.

HTTP makes the same distinction.

---

## What Client Errors Really Mean

Whenever a server returns a **4xx** status code,

it is telling the client:

> "I understood your request,

but I can't successfully process it because of something about your request."

The important idea is:

the problem is on the **client side**.

---

## Common Client Errors

Although every **4xx** response represents a client-side problem,

different status codes describe different kinds of problems.

### 400 Bad Request

```text
Request Is Invalid
```

The server cannot understand or process the request because something about it is incorrect.

Examples include:

- invalid request format,
- missing required fields,
- invalid values.

---

### 401 Unauthorized

```text
Authentication Required
```

The client hasn't proved its identity.

For example,

the user hasn't logged in,

or the authentication token is missing or invalid.

---

### 403 Forbidden

```text
Authenticated

↓

Not Allowed
```

The server knows who the client is,

but the client doesn't have permission to perform the requested operation.

---

### 404 Not Found

```text
Requested Resource

↓

Doesn't Exist
```

The server couldn't find the requested resource.

Examples include:

- a deleted user,
- a missing product,
- or an invalid URL.

---

## Connecting It To Android

When a Retrofit request completes,

your application may receive one of these responses.

Instead of treating them all the same,

the application often reacts differently.

For example,

a **401** response might navigate the user to the login screen,

while a **404** response might display:

> "The requested item could not be found."

Understanding the status code helps the application choose the appropriate response.

---

## The Bigger Picture

Every **4xx** response communicates the same core idea.

```text
Server Understood Request
            ↓
Problem Exists In The Request
            ↓
           4xx
```

The specific status code explains what kind of client-side problem occurred.

---

## Production Recognition

When reading production code,

reason like this.

```text
4xx
    ↓
Client Problem

↓

Which Kind?
```

Then use the specific status code to understand the exact issue.

---

## Revision

### Mental Model

```text
Server Understood Request
            ↓
Problem Exists In The Request
            ↓
           4xx
```

### Common Client Errors

```text
400
    ↓
Invalid Request

------------------------

401
    ↓
Authentication Required

------------------------

403
    ↓
Authenticated
    +
Not Allowed

------------------------

404
    ↓
Resource Not Found
```

### Remember

A **4xx** response means the server understood the request,

but couldn't process it because of something about the client's request.

---

## One Remaining Question

We've learned what happens when the problem lies with the client.

A natural question appears.

What if the client sends a perfectly valid request,

but something goes wrong on the server itself?

That leads us to the next concept.

```text
5xx Server Errors
```
