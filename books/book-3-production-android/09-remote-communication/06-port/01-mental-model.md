# Port

## Observation

In the previous chapter,

we learned how DNS helps a client find the IP address of a server.

A natural question appears.

Once the client reaches the correct computer,

how does it know **which application** on that computer should receive the request?

A single computer can run many applications at the same time.

For example,

- a web server,
- a database,
- an email server,
- a game server.

All of them share the same IP address.

How does the operating system deliver incoming data to the correct application?

---

## A Thought Experiment

Imagine a large apartment building.

The building has one street address.

Many families live inside.

If you simply write:

```text
221B Baker Street
```

the courier knows which building to visit.

But once they arrive,

they still have a question.

> "Which apartment?"

The building address identifies the building.

The apartment number identifies the specific home inside it.

Computers have the same problem.

An IP address identifies the computer.

Something else must identify the application running on that computer.

---

## Port

A **Port** is a number that identifies a specific application or service running on a computer.

Conceptually,

it looks like this.

```text
IP Address
        ↓
Which Computer?

------------------------

Port
        ↓
Which Application?
```

Together,

the IP address and the port identify exactly where incoming data should be delivered.

---

## An Example

Imagine a server with the IP address:

```text
203.0.113.10
```

That server might be running:

```text
Port 80
        ↓
Web Server

------------------------

Port 3306
        ↓
Database

------------------------

Port 6379
        ↓
Redis Cache
```

Every application listens on its own port.

When data arrives,

the operating system looks at the port number and delivers the data to the correct application.

---

## Why This Matters

Without ports,

every application on a computer would receive every incoming request.

The operating system wouldn't know where the data belongs.

Ports solve this problem by giving each application its own communication endpoint.

---

## Connecting It To Android

When your Android application sends an HTTP request,

it doesn't connect only to an IP address.

It also connects to a specific port.

For example,

```text
https://api.example.com:443
```

Here,

the client is connecting to:

- the computer identified by `api.example.com`,
- the application listening on port **443**.

Most of the time,

this port is chosen automatically,

so Android developers rarely need to specify it manually.

---

## The Bigger Picture

Finding a server happens in two steps.

```text
Domain Name
        ↓
DNS
        ↓
IP Address
        ↓
Which Computer?

------------------------

Port
        ↓
Which Application?
```

Only after both are known can communication begin.

---

## Production Recognition

Whenever you see a URL like:

```text
https://example.com:8080
```

don't think:

> "8080 is just another number."

Instead think:

> "The client is connecting to a specific application running on that computer."

The port identifies the destination application.

---

## Revision

### Mental Model

```text
IP Address
        ↓
Which Computer?

------------------------

Port
        ↓
Which Application?
```

### Remember

An IP address identifies a computer.

A port identifies an application running on that computer.

Both are required to deliver data to the correct destination.

---

## One Remaining Question

We've learned how a client identifies:

- the correct computer,
- and the correct application.

A natural question appears.

How does the client actually establish a communication channel with that application?

That leads us to the next concept.

```text
Socket
```
