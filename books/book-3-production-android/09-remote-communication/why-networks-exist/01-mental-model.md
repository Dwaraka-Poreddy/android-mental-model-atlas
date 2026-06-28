# Why Networks Exist

## Observation

Throughout the first two books of the Atlas,

almost everything happened inside a single computer.

```text
CPU

↓

Memory

↓

Program

↓

State

↓

UI
```

Even when we learned about coroutines,

Flow,

ViewModel,

and Compose,

everything was still happening inside the user's device.

A natural question appears.

```text
What If The Information

I Need

Doesn't Exist

On This Computer?
```

---

## A Thought Experiment

Imagine you're building a weather application.

The user opens the app and asks:

> "What's the weather in Tokyo?"

Your application immediately has a problem.

```text
Phone

↓

Doesn't Know
```

The phone has:

- no weather data,
- no satellite information,
- no knowledge of Tokyo's current temperature.

It simply cannot answer the question on its own.

---

## The Same Problem Everywhere

Weather isn't special.

The exact same problem appears in almost every modern application.

A messaging application needs messages.

A banking application needs account balances.

A shopping application needs product information.

A social media application needs posts.

A music application needs songs.

None of this information lives inside the user's phone.

Instead,

it lives somewhere else.

---

## The Fundamental Problem

We can summarize the situation like this.

```text
Computer A

Needs Information

That Exists On

Computer B
```

Now a much bigger question appears.

```text
How Can

One Computer

Talk To

Another Computer?
```

This is the problem that computer networks solve.

---

## Why Copying Files Isn't Enough

Someone might suggest:

> "Why not simply copy all the information onto every phone?"

That works for small applications.

But imagine trying to copy:

- YouTube videos,
- Instagram posts,
- Google Maps,
- bank transactions,
- online shopping catalogs,

onto every user's device.

Even if it were possible,

the information changes every second.

Keeping billions of devices synchronized would be practically impossible.

The data needs to remain in one place,

while still being accessible from anywhere.

---

## The Birth Of Networks

Instead of copying information everywhere,

computers became connected.

```text
Computer A

⇄

Computer B
```

Now,

instead of storing every piece of information locally,

a computer can simply ask another computer for it.

```text
Need Information

↓

Ask The Computer

That Owns It
```

This simple idea changed computing forever.

Applications were no longer limited to the information stored on one machine.

They could access information from computers anywhere in the world.

---

## Connecting It To Android

Every Android application you've ever used depends on this idea.

When you refresh Instagram,

your phone is asking another computer for new posts.

When you open Spotify,

your phone is asking another computer for music information.

When you check your bank balance,

your phone is asking another computer for the latest account data.

None of these applications work because your phone already knows everything.

They work because your phone knows how to communicate with other computers.

---

## The Bigger Picture

Computer networks didn't exist because programmers wanted a new technology.

They existed because computers needed a way to share information.

```text
Computer A

Needs Data

From

Computer B

↓

Network
```

Everything we'll study in this document—

HTTP,

APIs,

JSON,

Retrofit,

and networking libraries—

exists to solve this one fundamental problem.

---

## Production Recognition

Whenever you see code like:

```kotlin
api.getUser()

api.getProducts()

api.login()
```

don't immediately think:

```text
Retrofit
```

Instead,

think:

```text
My Application

Needs Information

From

Another Computer
```

The technology may change.

The problem never does.

---

## Revision

### The Problem

```text
Computer A

Needs Information

From

Computer B
```

### The Solution

```text
Connect Computers

↓

Network
```

### Mental Model

```text
Networks Exist

↓

So Computers

Can Share Information
```

---

## One Remaining Question

We've learned why computer networks exist.

A natural question appears.

```text
If Millions Of Computers

Can Communicate,

↓

How Does One Computer

Know

Which Computer

To Ask?
```

That leads us to the next concept.

```text
Client & Server
```
