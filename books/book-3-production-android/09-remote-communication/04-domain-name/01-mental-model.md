# Domain Name

## Observation

In the previous chapter,

we learned that every computer on a network has a unique IP address.

For example,

```text
142.250.183.206
```

A client can use this address to communicate with a server.

A natural question appears.

Would you rather remember:

```text
142.250.183.206
```

or

```text
google.com
```

Most people would choose the second.

---

## A Thought Experiment

Imagine your phone contacts several services every day.

A weather service.

A banking service.

A music service.

A shopping service.

Now imagine you had to remember the numeric address of every one of them.

```text
142.250.183.206

140.82.121.4

172.217.167.78

104.18.32.47
```

That quickly becomes impractical.

Numbers work well for computers.

They don't work well for humans.

---

## The Same Problem Everywhere

This problem isn't unique to computers.

Imagine if your friends were known only by phone numbers.

Instead of saying:

> "Call Rahul."

you would say:

> "Call 9182736450."

Technically,

it works.

But names are much easier to remember than long sequences of digits.

The same idea applies to computers.

---

## Domain Names

A **Domain Name** is simply a human-friendly name that represents a computer or service on a network.

For example,

```text
google.com

github.com

openai.com

amazon.in
```

Instead of remembering an IP address,

people remember a domain name.

---

## What A Domain Name Is Not

A common misunderstanding is that a domain name **replaces** an IP address.

It doesn't.

The computer still communicates using an IP address.

The domain name simply gives humans an easier way to refer to that computer.

Think of it like this.

```text
Humans
      ↓
Domain Name
      ↓
IP Address
      ↓
Computer
```

The domain name is for us.

The IP address is for the network.

---

## Connecting It To Android

When you create a Retrofit instance,

you typically write something like:

```kotlin
Retrofit.Builder()
    .baseUrl("https://api.example.com/")
    .build()
```

Notice that you don't provide an IP address.

You provide a domain name.

Somewhere underneath,

Android still needs the server's IP address before it can send a request.

That raises another interesting question.

---

## The Bigger Picture

Domain names solve a human problem,

not a networking problem.

```text
Computers Prefer Numbers
        ↓
Humans Prefer Names
        ↓
Domain Names
```

They make the Internet easier to use,

while allowing computers to continue communicating using IP addresses.

---

## Production Recognition

Whenever you see:

```text
api.github.com

maps.googleapis.com

api.openai.com
```

don't think:

> "That's the server."

Instead think:

> "That's the human-friendly name of the server."

The actual communication still happens using an IP address.

---

## Revision

### Mental Model

```text
Humans
      ↓
Domain Name
      ↓
IP Address
      ↓
Computer
```

### Remember

A domain name is a human-friendly name.

It does not replace the IP address.

It simply makes computers easier for humans to identify.

---

## One Remaining Question

We've learned that humans use domain names,

while computers communicate using IP addresses.

A natural question appears.

How does a computer convert:

```text
google.com
```

into the correct IP address?

That leads us to the next concept.

```text
DNS
```
