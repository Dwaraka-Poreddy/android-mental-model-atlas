# Authentication vs Authorization

## Observation

In the previous chapter,

we learned that RESTful APIs expose many endpoints.

Some endpoints are public.

Others should be accessible only to specific users.

For example,

```text
GET /news
```

might be available to everyone.

But

```text
GET /profile

POST /transfer-money

GET /admin/dashboard
```

shouldn't be accessible to every client.

A natural question appears.

Before a Web Service decides whether a request is allowed,

it must first answer two completely different questions.

```text
Who Are You?

↓

What Are You Allowed To Do?
```

Although these questions sound similar,

they solve two different problems.

---

## A Thought Experiment

Imagine entering a company's office building.

At the reception,

you're asked to show your employee ID.

If your identity is verified,

you're allowed to enter the building.

But entering the building doesn't mean you can go everywhere.

Some employees can access:

- the engineering floor,
- the finance department,
- the server room.

Others cannot.

The office makes two separate decisions.

First,

it verifies **who you are**.

Then,

it decides **where you're allowed to go**.

Web Services solve exactly the same problem.

---

## The Problem

Before performing a protected operation,

a Web Service must answer two questions.

```text
Who Is Making This Request?

↓

Authentication

------------------------

Can This User Perform This Operation?

↓

Authorization
```

These are separate decisions.

Knowing someone's identity doesn't automatically grant them every permission.

---

## Authentication

**Authentication** is the process of verifying a user's identity.

It answers the question:

```text
Who Are You?
```

For example,

when you log in using:

- an email and password,
- a fingerprint,
- Face ID,

the Web Service verifies that you are who you claim to be.

If the verification succeeds,

the user is authenticated.

---

## Authorization

**Authorization** determines what an authenticated user is allowed to do.

It answers the question:

```text
What Are You Allowed To Do?
```

For example,

an application may define different roles.

```text
User

Moderator

Administrator
```

All three users may be authenticated.

But they don't all have the same permissions.

An administrator might be allowed to delete users.

A normal user cannot.

Authorization decides which operations are permitted.

---

## Authentication Comes First

These two concepts always happen in order.

```text
Authentication

↓

Identity Verified

↓

Authorization

↓

Permissions Checked
```

A Web Service can't decide what you're allowed to do,

until it knows who you are.

---

## Connecting It To Android

Imagine a banking application.

When the user logs in,

the server authenticates their identity.

Later,

when the user requests:

```text
POST /transfer-money
```

the server checks whether that authenticated user has permission to perform the transfer.

Similarly,

an administrator may access:

```text
GET /admin/dashboard
```

while a normal user receives an error.

---

## The Bigger Picture

Authentication and Authorization solve different problems.

```text
Authentication

↓

Identity

------------------------

Authorization

↓

Permissions
```

One verifies who the user is.

The other determines what the user is allowed to do.

---

## Production Recognition

When reading backend documentation,

you'll often encounter statements like:

- "This endpoint requires authentication."
- "Administrator access required."
- "Insufficient permissions."

You can now recognize what they mean.

```text
Authentication

↓

Identity
------------------------
Authorization

↓

Permission
```

---

## Revision

### Mental Model

```text
Authentication

↓

Who Are You?

------------------------

Authorization

↓

What Are You Allowed To Do?
```

### Remember

Authentication verifies identity.

Authorization determines permissions.

A user must be authenticated before they can be authorized.

---

## One Remaining Question

We've learned how a Web Service verifies a user's identity.

A natural question appears.

REST encourages every request to be independent.

If every request is processed independently,

how can the server recognize the same authenticated user across thousands of requests,

without asking them to log in every single time?

That leads us to the next concept.

```text
Tokens
```
