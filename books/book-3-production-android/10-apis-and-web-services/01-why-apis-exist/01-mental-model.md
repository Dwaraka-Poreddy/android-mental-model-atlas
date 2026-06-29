# Why APIs Exist

## Observation

In the previous part,

we learned how two computers communicate.

A client can send requests.

A server can send responses.

A natural question appears.

Now that communication is possible,

what exactly is the client allowed to ask the server to do?

Can it request absolutely anything?

Can it execute any function inside the server?

Can it access every piece of data?

Or does the server expose only a carefully chosen set of operations?

---

## A Thought Experiment

Imagine visiting a restaurant.

The kitchen knows how to prepare hundreds of different dishes.

It has ingredients,

recipes,

and cooking techniques.

But when you sit down,

you're not allowed to request anything you can imagine.

Instead,

the restaurant gives you a menu.

The menu defines exactly what customers are allowed to order.

Everything else remains inside the kitchen.

Servers solve the same problem.

A server may contain thousands of internal functions,

but it shouldn't expose all of them to the outside world.

Instead,

it exposes only a carefully chosen set of operations.

---

## The Problem

Imagine a banking application.

Internally,

it may have functions like:

- calculateInterest()
- updateAccountBalance()
- validateTransaction()
- transferMoney()

These functions are implementation details.

Other applications shouldn't call them directly.

Instead,

the banking application should expose only carefully designed operations.

---

## A Controlled Interface

Applications solve this by exposing a limited set of operations that other applications are allowed to use.

Instead of exposing their entire implementation,

they expose only a carefully designed interface.

This keeps the internal implementation private,

while allowing other applications to interact with them safely.

---

## The Bigger Picture

Communication alone isn't enough.

```text
HTTP
        ↓
Move Messages

------------------------

API
        ↓
Expose Functionality
```

HTTP answers:

> "How do messages travel?"

APIs answer:

> "What operations are available?"

These solve completely different problems.

---

## Production Recognition

Whenever an Android application communicates with a server,

it isn't interacting with the server's internal implementation.

Instead,

it communicates with the server's public interface.

That public interface is the next concept we'll study.

---

## Revision

### Mental Model

```text
HTTP
        ↓
Communication

------------------------

API
        ↓
Public Interface
```

### Remember

HTTP allows two applications to communicate.

An API defines what one application allows another application to do.

---

## One Remaining Question

We've learned why applications need a controlled interface.

A natural question appears.

What exactly is an API?

That leads us to the next concept.

```text
API
```
