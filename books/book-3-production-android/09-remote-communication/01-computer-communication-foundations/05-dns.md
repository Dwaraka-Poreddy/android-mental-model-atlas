# DNS

## Observation

In the previous chapter,

we learned that humans prefer domain names,

while computers communicate using IP addresses.

For example,

a human types:

```text
google.com
```

but the computer eventually needs something like:

```text
142.250.183.206
```

A natural question appears.

How does the computer convert one into the other?

---

## A Thought Experiment

Imagine you want to call your friend.

You know their name.

```text
Rahul
```

But your phone doesn't make calls using names.

It needs a phone number.

So what do you do?

You search your contacts.

```text
Rahul
      ↓
Search Contacts
      ↓
+91 98765 43210
      ↓
Call
```

Your contacts act like a directory.

They translate a human-friendly name into something the phone can actually use.

Computers face the same problem.

---

## The Internet Needs A Directory

The Internet has millions of computers.

Each one has an IP address.

Humans,

however,

prefer names.

There needs to be a system that translates:

```text
google.com
```

into

```text
142.250.183.206
```

That system is called the **Domain Name System**, or **DNS**.

---

## What DNS Does

DNS is simply a lookup system.

You give it a domain name.

It returns the corresponding IP address.

Conceptually,

it works like this.

```text
Domain Name
      ↓
DNS Lookup
      ↓
IP Address
```

Once the IP address is known,

the computer can communicate with the correct server.

---

## Connecting It To Android

Suppose your application wants to call:

```text
https://api.example.com
```

Your application does **not** immediately send an HTTP request.

First,

Android needs to determine the server's IP address.

Conceptually,

the sequence looks like this.

```text
api.example.com
      ↓
DNS
      ↓
203.0.113.25
      ↓
Connect To Server
```

Only after the IP address is known can communication begin.

---

## Does DNS Happen Every Time?

A natural question appears.

If every request requires DNS,

wouldn't the Internet become slow?

Fortunately,

no.

Operating systems,

browsers,

and networking libraries cache DNS results for some time.

This means repeated requests often reuse a previously resolved IP address instead of performing another DNS lookup.

As Android developers,

we usually don't manage this ourselves.

It happens automatically underneath the networking stack.

---

## The Bigger Picture

DNS doesn't replace IP addresses.

It simply makes them easier to use.

Think of the relationship like this.

```text
Humans
      ↓
Domain Name
      ↓
DNS
      ↓
IP Address
      ↓
Computer
```

Every piece has a single responsibility.

- Humans remember names.
- DNS translates names.
- Computers communicate using IP addresses.

---

## Production Recognition

Whenever you write:

```kotlin
Retrofit.Builder()
    .baseUrl("https://api.example.com/")
    .build()
```

the domain name isn't used directly for communication.

Before any network request is sent,

Android performs DNS resolution to obtain the server's IP address.

Most networking libraries hide this step,

but it happens every time a new connection needs to locate the server.

---

## Revision

### Mental Model

```text
Domain Name
      ↓
DNS
      ↓
IP Address
      ↓
Computer
```

### Remember

DNS is not a networking protocol for exchanging application data.

It is a lookup system that translates human-friendly domain names into IP addresses.

---

## One Remaining Question

We've learned how a client discovers the server's IP address.

A natural question appears.

Now that the client knows:

- who to talk to,
- and where to find them,

how do the two computers actually exchange information?

That leads us to the next concept.

```text
HTTP
```
