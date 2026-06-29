# REST & RESTful APIs

## Observation

In the previous chapter,

we learned that a Web Service exposes multiple endpoints.

A natural question appears.

Imagine three different companies building similar Web Services.

Company A exposes:

```text
/getUsers

/createUser

/deleteUser
```

Company B exposes:

```text
/users/get

/users/create

/users/delete
```

Company C exposes:

```text
/userService?action=get

/userService?action=create

/userService?action=delete
```

All of these work.

But every Web Service looks completely different.

As a developer,

you would need to learn a new design for every API you use.

Wouldn't it be better if most Web Services followed a common set of conventions?

This is the problem REST tries to solve.

---

## The Problem

A Web Service can expose any number of endpoints.

Without common design principles,

every team would organize those endpoints differently.

This makes APIs:

- difficult to learn,
- difficult to document,
- difficult to maintain,
- and difficult to integrate with.

REST introduces a common way of designing Web Services.

---

## REST

**REST (Representational State Transfer)** is an architectural style for designing Web Services.

Rather than defining a protocol,

REST provides a set of design principles that help Web Services remain consistent,

predictable,

and easy to understand.

Conceptually,

it looks like this.

```text
HTTP
        ↓
Communication

------------------------

REST
        ↓
Organization
```

HTTP defines **how** requests and responses travel.

REST suggests **how** Web Services should be designed.

---

## RESTful APIs

A **RESTful API** is simply a Web Service that follows REST's design principles.

```text
REST Principles
        ↓
Applied To
A Web Service
        ↓
RESTful API
```

Not every Web Service is RESTful.

REST is one way of designing Web Services,

not the only way.

---

## Thinking In Resources

One of REST's biggest ideas is to think in terms of **resources** rather than actions.

A resource is simply something your application manages.

For example:

- Users
- Orders
- Products
- Payments
- Messages

Instead of designing endpoints around actions,

REST encourages designing them around resources.

This makes APIs more predictable and easier to understand.

---

## REST Uses HTTP Methods

Earlier,

we learned about HTTP methods such as:

- GET
- POST
- PUT
- PATCH
- DELETE

REST doesn't invent new methods.

Instead,

it gives them consistent meaning.

```text
GET
        ↓
Read

POST
        ↓
Create

PUT
        ↓
Replace

PATCH
        ↓
Update

DELETE
        ↓
Delete
```

As a result,

developers can often understand what an endpoint does just by looking at its HTTP method.

---

## Resource-Oriented URLs

REST also encourages URLs to represent resources.

For example,

Good:

```text
/users

/orders

/products
```

Less preferred:

```text
/getUsers

/createUser

/deleteUser
```

Notice that the URL identifies **what** is being accessed,

while the HTTP method describes **what operation** should be performed.

Together,

they create APIs that are easier to read and reason about.

---

## Stateless Communication

Another important REST principle is **statelessness**.

Each request should contain everything the server needs to process it.

The server should not rely on information remembered from previous requests.

Conceptually,

every request should be independent.

```text
Request 1

✓ Complete

------------------------

Request 2

✓ Complete

------------------------

Request 3

✓ Complete
```

This makes Web Services easier to scale,

more reliable,

and simpler to maintain.

---

## REST Isn't The Only Choice

REST is the most common architectural style you'll encounter in Android development,

but it isn't the only one.

Other approaches include:

- SOAP
- GraphQL
- gRPC

Each solves similar problems in different ways.

This Atlas focuses on REST because it is by far the approach Android developers encounter most frequently in production applications.

The important idea is:

```text
Web Services

├── REST
├── SOAP
├── GraphQL
└── gRPC
```

REST is one design style,

not the only design style.

---

## Why REST Became Popular

REST became widely adopted because it builds on technologies developers already use,

such as HTTP,

URLs,

and standard HTTP methods.

Its conventions make APIs easier to:

- understand,
- document,
- maintain,
- and consume.

This consistency is one of the main reasons why REST remains the dominant style for Web Services today.

---

## Production Recognition

When reading backend API documentation,

you'll often see endpoints like:

```text
GET    /users

POST   /orders

PATCH  /profile

DELETE /cart/{id}
```

You can now recognize that:

- the URL identifies the resource,
- the HTTP method identifies the operation,
- and together they form a RESTful endpoint.

---

## Revision

### Mental Model

```text
HTTP
        ↓
Communication

+

REST
        ↓
Design Principles

↓

RESTful API
```

### Remember

REST is an architectural style for designing Web Services.

A RESTful API is a Web Service that follows those design principles.

---

## One Remaining Question

Many RESTful APIs don't allow every client to access every endpoint.

Some endpoints require users to log in.

Others are available only to administrators.

How does a Web Service identify who is making the request,

and decide what they're allowed to do?

That leads us to the next concept.

```text
Authentication vs Authorization
```
