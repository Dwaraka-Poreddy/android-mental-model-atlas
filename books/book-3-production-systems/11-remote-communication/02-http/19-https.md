# HTTPS

## Observation

In the previous chapters,

we learned how two computers communicate using HTTP.

A client sends a request.

The server processes it.

The server returns a response.

A natural question appears.

What happens to those messages while they're traveling across the internet?

Can someone else see them?

Can someone even modify them before they reach the destination?

---

## A Thought Experiment

Imagine sending a postcard through the mail.

Anyone who handles that postcard can read its contents.

The postal workers,

sorting centers,

and anyone else who gets hold of it

can all see the message.

Now imagine writing your online banking password on that postcard.

Would you feel comfortable sending it?

Probably not.

Computers face exactly the same problem.

An ordinary HTTP request is like sending a postcard.

If someone can observe the network,

they can potentially read the information being transmitted.

---

## The Problem With HTTP

By itself,

HTTP focuses on communication.

It doesn't provide protection.

If sensitive information is sent using plain HTTP,

someone observing the network may be able to see:

- usernames,
- passwords,
- authentication tokens,
- personal information,
- payment details.

This makes plain HTTP unsuitable for most modern applications.

A safer solution is needed.

---

## HTTPS

**HTTPS** is HTTP with a secure communication channel.

Instead of sending messages as plain text,

the client and server first establish a secure connection.

Only then do they exchange HTTP requests and responses.

Conceptually,

it looks like this.

```text
HTTP
        ↓
Secure Communication
        ↓
HTTPS
```

The communication is still HTTP.

The difference is that the data is now protected while traveling across the network.

---

## What HTTPS Protects

HTTPS provides three important guarantees.

### Privacy

```text
Only The Client
        +
The Server

Can Read
The Data
```

Someone observing the network cannot easily read the communication.

---

### Integrity

```text
Data Sent
        ↓
Data Received
```

The client and server can detect if the data was modified while traveling across the network.

---

### Identity

The client can verify that it is communicating with the intended server,

not someone pretending to be that server.

---

## Connecting It To Android

Whenever you see a URL such as:

```text
https://api.example.com
```

your Android application is communicating over HTTPS.

Libraries such as **OkHttp** automatically perform the work required to establish the secure connection before any HTTP messages are exchanged.

Most Android developers never implement this security themselves.

The networking library handles it automatically.

---

## The Bigger Picture

HTTP and HTTPS solve different problems.

```text
HTTP
        ↓
Communication

------------------------

HTTPS
        ↓
Secure Communication
```

HTTPS doesn't replace HTTP.

It builds upon it by adding security.

---

## Production Recognition

Whenever you see:

```text
https://
```

don't think:

> "That's just part of the URL."

Instead think:

> "This communication is protected while traveling across the network."

The application is still using HTTP,

but over a secure connection.

---

## Revision

### Mental Model

```text
HTTP
        ↓
Communication

+

Security

↓

HTTPS
```

### Remember

HTTP enables communication.

HTTPS enables secure communication.

The messages remain HTTP messages,

but they are protected while traveling across the network.

---

## One Remaining Question

We've learned how applications communicate securely with remote servers.

A natural question appears.

How do applications expose functionality that other applications can call over HTTP?

That leads us to the next part.

```text
APIs & Web Services
```
