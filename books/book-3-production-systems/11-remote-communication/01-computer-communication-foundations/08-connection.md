# Connection

## Observation

In the previous chapter,

we learned that a socket is one endpoint of a communication channel.

A natural question appears.

If the client has a socket,

and the server also has a socket,

can they immediately start exchanging data?

Not yet.

The two sockets must first be connected.

---

## A Thought Experiment

Imagine making a phone call.

You have a phone.

Your friend also has a phone.

Simply owning two phones doesn't mean you're talking.

Someone must place the call.

The other person must answer.

Only then is the call connected.

After that,

both people can speak freely.

Computers work in exactly the same way.

Having two sockets isn't enough.

They must first establish a connection.

---

## Connection

A **Connection** is an active communication link between two sockets.

Once the connection is established,

both applications can exchange data.

Conceptually,

it looks like this.

```text
Client Application
        │
     Socket
        │
════════╪════════
        │
     Socket
        │
Server Application
```

The connection links the two sockets together,

creating a path through which data can travel.

---

## Why Connections Exist

Imagine trying to send data before a connection exists.

The operating system wouldn't know:

- whether the other application is available,
- whether it's ready to receive data,
- or where the incoming data should be delivered.

By establishing a connection first,

both sides agree that they're ready to communicate.

Only then does data begin to flow.

---

## Connecting It To Android

When your Android application makes an HTTP request,

networking libraries such as **OkHttp** first establish a connection to the server.

Only after that connection exists do they begin sending:

- the HTTP request,
- any request body,
- and eventually receive the HTTP response.

Most Android developers never manage connections directly.

The networking library handles them automatically.

---

## The Bigger Picture

Every networking concept we've learned builds on the previous one.

```text
IP Address
        ↓
Find The Computer

------------------------

Port
        ↓
Find The Application

------------------------

Socket
        ↓
Communication Endpoint
