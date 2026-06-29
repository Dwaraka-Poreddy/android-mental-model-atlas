# Endpoint

## Observation

In the previous chapter,

we learned that a Web Service exposes its API over a network.

A natural question appears.

A Web Service usually doesn't expose just one operation.

For example,

a banking Web Service might allow clients to:

- log in,
- view an account,
- transfer money,
- view transaction history,
- update a profile.

If a client wants to perform only one of these operations,

how does it specify exactly which one it wants?

---

## A Thought Experiment

Imagine calling a company's customer support number.

After the call connects,

you're presented with a menu.

```text
Press 1
Sales

Press 2
Technical Support

Press 3
Billing
```

You're still communicating with the same company.

The only difference is that you've chosen one specific service.

Web Services work in a very similar way.

A single Web Service may expose many operations.

The client needs a way to choose exactly one of them.

---

## The Problem

A Web Service is like a collection of operations.

Without a way to identify a specific operation,

the server wouldn't know what the client is asking it to do.

The client must be able to say:

- "I want to log in."
- "I want my profile."
- "I want to transfer money."

Each operation needs its own identity.

---

## Endpoint

An **Endpoint** represents one specific operation exposed by a Web Service.

Conceptually,

it looks like this.

```text
Web Service
        │
        ├── Login
        ├── Get Profile
        ├── Transfer Money
        ├── Transaction History
        └── Update Profile
```

Each operation is an endpoint.

The client chooses the endpoint that performs the operation it needs.

---

## How Clients Reach An Endpoint

Every endpoint must be reachable over the network.

In HTTP,

an endpoint is identified using a URL.

For example,

```text
/users

/orders

/profile
```

These URLs identify different operations exposed by the same Web Service.

The important idea isn't the URL itself.

The important idea is that each URL represents a different operation.

---

## Web Service vs Endpoint

These concepts describe different things.

```text
Web Service
        ↓
Collection Of Operations

------------------------

Endpoint
        ↓
One Specific Operation
```

A single Web Service may expose dozens,

or even hundreds,

of endpoints.

---

## Connecting It To Android

When your Android application requests:

```text
GET /profile
```

it isn't communicating with an entire Web Service in a generic way.

It is calling one specific endpoint exposed by that Web Service.

Different endpoints perform different operations,

even though they all belong to the same Web Service.

---

## The Bigger Picture

Notice how the concepts continue building on one another.

```text
API
        ↓
Public Interface

↓

Web Service
        ↓
API Available
Over A Network

↓

Endpoint
        ↓
One Specific Operation
```

Each concept answers a different question.

---

## Production Recognition

When reading backend documentation,

you'll often see lists like:

```text
GET /users

POST /orders

DELETE /cart/{id}
```

Don't think of these as random URLs.

Think of them as the individual operations that the Web Service exposes.

Each one is an endpoint.

---

## Revision

### Mental Model

```text
Web Service
        ↓
Collection Of Operations

↓

Endpoint
        ↓
One Specific Operation
```

### Remember

An endpoint represents one specific operation exposed by a Web Service.

A Web Service contains many endpoints.

---

## One Remaining Question

We've learned that a Web Service exposes many endpoints.

A natural question appears.

If different teams design endpoints in completely different ways,

wouldn't every Web Service look different?

Is there a common set of principles for designing Web Services?

That leads us to the next concept.

```text
REST
```
