# Client & Server

## Observation

In the previous chapter,

we learned why computer networks exist.

```text
Computer A Needs Information From Computer B
```

A natural question appears.

```text
If Every Computer Can Talk To Every Other Computer,

↓

How Does One Computer Know Who Owns The Information It Needs?
```

---

## A Thought Experiment

Imagine every computer on Earth stored a small piece of information.

One stores weather data.

Another stores bank accounts.

Another stores videos.

Another stores music.

Now imagine your computer needs today's weather.

```text
Millions Of Computers

↓

Which One Should It Ask?
```

Without some kind of organization,

every computer would have to search the entire world before finding the answer.

That clearly doesn't scale.

---

## Dividing Responsibilities

Instead of every computer doing everything,

networks divide responsibilities.

Some computers primarily **provide** information.

Others primarily **request** information.

```text
Provide Information

↓

Server

------------------------

Request Information

↓

Client
```

Now every computer has a clear role.

---

## The Client

A **client** is simply a computer that requests information or services from another computer.

For example,

your Android phone asks for:

- weather,
- messages,
- videos,
- account balances.

Your phone is acting as a client.

Notice something important.

A client does **not** own the information.

It only needs it.

---

## The Server

A **server** is a computer whose primary responsibility is to provide information or services to other computers.

For example,

a weather server stores weather information.

A banking server stores account information.

A music server stores songs.

The server owns the information.

Clients ask for it.

---

## Connecting Them Together

Once we have both roles,

communication becomes much simpler.

```text
Client

↓

Requests Information

↓

Server

↓

Returns Information

↓

Client
```

Instead of asking every computer,

the client asks the computer responsible for that information.

---

## Connecting It To Android

Every Android application you've ever used follows this model.

When you open Instagram,

your phone becomes the client.

Instagram's computers become the servers.

When you check your bank balance,

your phone is the client.

Your bank's computers are the servers.

The same pattern appears everywhere.

---

## The Bigger Picture

Client and Server are **roles**,

not specific devices.

Today's client could become tomorrow's server.

A laptop can be a client.

A phone can be a server.

A cloud computer can be both.

The names describe **responsibilities**,

not hardware.

```text
Requests Information

↓

Client

------------------------

Owns Information

↓

Server
```

---

## Production Recognition

Whenever you see code like:

```kotlin
userApi.getUser()

productApi.getProducts()

loginApi.login()
```

don't immediately think:

```text
Retrofit
```

Instead,

think:

```text
My Application Is Acting As A Client Talking To A Server
```

The library is simply helping the client communicate.

---

## Revision

### Responsibilities

```text
Client

↓

Requests Information
```

```text
Server

↓

Owns Information

↓

Provides Information
```

### Mental Model

```text
Client

↓

Asks

------------------------

Server

↓

Answers
```

---

## One Remaining Question

We've learned that a client requests information from a server.

A natural question appears.

```text
If The Client Wants Information,

↓

How Does It Tell The Server Exactly What It Wants?
```

That leads us to the next concept.

```text
HTTP
```
