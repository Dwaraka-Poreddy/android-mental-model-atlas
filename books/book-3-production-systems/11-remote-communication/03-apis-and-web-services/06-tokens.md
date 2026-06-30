# Tokens

## Observation

In the previous chapter,

we learned that a Web Service first authenticates a user,

and then determines what that user is allowed to do.

A natural question appears.

Imagine opening your banking application.

You log in once.

Then you:

- view your profile,
- check your balance,
- transfer money,
- view your transaction history.

Each of these actions sends a completely separate HTTP request.

If REST encourages every request to be independent,

does the user need to enter their username and password before every single request?

Obviously not.

So how does the Web Service recognize the same authenticated user across thousands of independent requests?

This is exactly the problem Tokens solve.

---

## A Thought Experiment

Imagine checking into a hotel.

When you arrive,

you first visit the reception desk.

The receptionist asks for your identification,

verifies your reservation,

and confirms who you are.

Only after your identity has been verified,

you're given a room key card.

From that point onward,

you don't need to return to the reception desk every time you want to enter your room.

You simply use the key card.

The hotel doesn't ask for your passport every single time.

The key card is enough.

The key card isn't your identity.

It is proof that your identity has already been verified.

A token plays the same role in a Web Service that a room key card plays in a hotel.

After a user successfully logs in,

the Web Service issues a token.

Future requests present that token,

allowing the server to recognize the authenticated user without asking them to log in again.

---

## The Problem

Authentication proves who you are.

But authentication usually happens only once,

when you log in.

After that,

every new HTTP request is completely independent.

The server cannot assume that two requests came from the same user.

Without some form of proof,

the server would have to authenticate the user again for every request.

This would be slow,

inefficient,

and frustrating for the user.

---

## Token

A **Token** is proof that a user has already been authenticated.

Instead of asking for a username and password every time,

the client sends the token with each request.

The server verifies the token,

recognizes the user,

and continues processing the request.

Conceptually,

it looks like this.

```text
User Logs In

↓

Authentication

↓

Token Issued

↓

Future Requests

↓

Identity Verified
```

The user's password is no longer needed for every request.

The token becomes the proof of identity.

---

## How Tokens Are Used

A typical interaction looks like this.

```text
User

↓

Login

↓

Server Verifies Credentials

↓

Token Issued

↓

Client Stores Token

↓

Future Requests Include Token

↓

Server Verifies Token

↓

Request Processed
```

Notice that authentication happens only once.

Every request afterwards uses the token instead of repeating the login process.

---

## Tokens And HTTP

Earlier,

we learned that HTTP requests contain:

- a URL,
- headers,
- an optional body.

Tokens are typically sent as part of the request headers.

A simplified request might look like this.

```http
GET /profile
Authorization: Bearer <token>
```

The important idea isn't the exact header name.

The important idea is that every request carries proof of the user's identity.

This allows the server to process each request independently,

while still recognizing the authenticated user.

---

## Tokens And Stateless REST

Earlier,

we learned that REST encourages stateless communication.

Every request should contain everything the server needs to process it.

Tokens fit perfectly into this idea.

Instead of the server remembering who is logged in,

every request brings its own proof.

```text
Request

+

Token

↓

Everything Needed
To Verify Identity
```

This allows every request to be processed independently,

while still supporting authenticated users.

---

## Common Token Implementations

A token is a concept,

not a specific technology.

Different systems implement tokens in different ways.

Some of the most common implementations are:

- JWT (JSON Web Token)
- Opaque Tokens
- Session IDs

Although these implementations work differently,

they all solve the same problem.

```text
Authentication

↓

Future Requests

↓

Proof Of Identity
```

Today,

JWTs are the most commonly used token format in modern Android applications.

This is why you'll often hear developers simply say:

> "The backend returns a JWT."

The important thing to remember is:

```text
Token

↓

Concept

------------------------

JWT

↓

One Implementation
```

JWT is one implementation of a token.

It is **not** the definition of a token.

Concepts such as:

- JWT internals,
- OAuth,
- Access Tokens,
- Refresh Tokens,
- Session Management,

introduce entirely new mental models,

so they are explored separately in the Atlas.

---

## Invalid Tokens

A token isn't valid forever.

For security reasons,

tokens may become:

- expired,
- revoked,
- invalid,
- or malformed.

When this happens,

the server rejects the request.

The client must usually authenticate again to obtain a new token.

This is why applications sometimes ask users to log in again after a period of inactivity.

---

## Putting Everything Together

Notice how all the concepts from this part connect together.

```text
User

↓

Login

↓

Authentication

↓

Token Issued

========================

HTTP Request

↓

Authorization Header

↓

Token

↓

Web Service

↓

Identity Verified

↓

Authorization

↓

Endpoint

↓

HTTP Response
```

This is a simplified version of what happens every time an authenticated Android application communicates with a backend server.

The concepts we've learned are no longer isolated.

They now work together as one complete system.

---

## Production Recognition

When reading Android production code,

you'll often encounter requests like:

```http
GET /profile
Authorization: Bearer eyJhbGciOiJIUzI1NiIs...
```

Or you may see networking code that automatically attaches a token before every request.

You can now recognize what's happening.

The application isn't sending the user's password repeatedly.

It's sending proof that the user has already been authenticated.

If the backend uses JWT,

that token happens to be a JWT.

Another backend might use an opaque token or a session ID.

The underlying idea remains exactly the same.

---

## Revision

### Mental Model

```text
User Logs In

↓

Authentication

↓

Token Issued

↓

Future Requests

↓

Identity Verified
```

### Remember

A token is proof that a user has already been authenticated.

It allows a Web Service to recognize the same user across many independent HTTP requests without requiring them to log in again.

JWT is one common implementation of a token,

not the definition of a token.

---

## Looking Back

You've now built a complete mental model of how Android applications communicate with remote servers.

```text
Network

↓

HTTP

↓

Requests & Responses

↓

Web Services

↓

REST

↓

Authentication

↓

Authorization

↓

Tokens
```

You now understand not only how applications communicate,

but also how they securely identify users while doing so.

---

## One Remaining Question

We've learned how clients and servers communicate,

how they identify users,

and how they securely exchange requests.

A natural question appears.

When a Web Service sends information such as:

- a user profile,
- an order,
- a list of products,
- or transaction history,

how is that information represented so that both the client and the server understand exactly the same data?

That leads us to the next part.

```text
Data Representation
```
