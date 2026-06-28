# IP Address

## Observation

In the previous chapter,

we learned that a client requests information from a server.

A natural question appeared.

Imagine your phone wants today's weather.

There are millions of servers connected to the Internet.

Which one should it contact?

Before a client can request any information,

it first needs a way to uniquely identify the server it wants to communicate with.

---

## A Thought Experiment

Imagine a city with millions of houses.

You want to send a letter to your friend.

You know:

- their name,
- what they look like,
- where they work.

But you don't know their address.

Can the postal service deliver your letter?

No.

The problem isn't that the postal service is broken.

The problem is that it doesn't know **where** to deliver the letter.

---

## Computers Have The Same Problem

Computers face exactly the same challenge.

A client may know:

> "I want today's weather."

But that isn't enough.

It still needs to know:

> "Which computer stores that information?"

Without a way to identify the destination,

the request has nowhere to go.

---

## Every Computer Needs An Address

Just like every house needs a postal address,

every computer connected to a network needs an address.

That address is called an **IP Address**.

An IP address uniquely identifies a computer on a network.

Without it,

other computers wouldn't know where to send requests.

---

## An Example

Imagine a weather server has the following IP address.

```text
142.250.183.206
```

When your phone wants weather information,

it sends the request to that address.

```text
Phone
   │
   │ Request
   ▼
142.250.183.206
```

The important idea isn't the numbers themselves.

The important idea is that the address uniquely identifies the destination computer.

---

## Connecting It To Android

Whenever your Android application requests data,

it eventually needs to communicate with a specific computer.

Somewhere underneath Retrofit,

OkHttp,

and every other networking library,

there is always an IP address identifying the destination.

Most of the time,

you never see it.

But it is always there.

---

## A Problem Appears

At first,

IP addresses seem like a perfect solution.

But imagine visiting websites like:

- google.com
- youtube.com
- github.com
- openai.com

Would you really want to remember addresses like:

```text
142.250.183.206

140.82.121.4

172.217.167.78
```

Probably not.

They're difficult for humans to remember.

Computers like numbers.

Humans prefer names.

This creates another problem.

---

## The Bigger Picture

An IP address solves one important problem.

```text
Every Computer Needs A Unique Address
        ↓
IP Address
```

It allows one computer to locate another.

But it doesn't solve the usability problem.

Humans still need an easier way to identify computers.

---

## Production Recognition

When you write:

```kotlin
val retrofit = Retrofit.Builder()
    .baseUrl("https://api.example.com/")
    .build()
```

you don't provide an IP address directly.

Instead,

you provide a human-friendly name.

Behind the scenes,

that name is eventually translated into an IP address before any request is sent.

We'll discover how that happens in the next chapter.

---

## Revision

### Mental Model

```text
Every Computer Needs A Unique Address
        ↓
IP Address
```

### Remember

An IP address is not a website.

It is not an API.

It is not a server.

It is simply the unique address that identifies a computer on a network.

---

## One Remaining Question

We've learned that every computer has a unique IP address.

A natural question appears.

If computers use IP addresses,

why do we type names like:

- google.com
- github.com
- openai.com

instead of long strings of numbers?

That leads us to the next concept.

```text
Domain Name
```
