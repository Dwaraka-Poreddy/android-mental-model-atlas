# API

## Observation

In the previous part,

we learned how two computers communicate.

A client can send requests.

A server can send responses.

A natural question appears.

Communication allows one application to send messages to another.

But what exactly is the client allowed to ask the server to do?

Can it execute any function inside the server?

Can it access every piece of data?

Or does the server expose only a carefully chosen set of operations?

---

## A Thought Experiment

Imagine driving a car.

The car contains thousands of mechanical parts.

- the engine,
- the transmission,
- the braking system,
- the fuel injectors.

Yet,

as the driver,

you don't interact with any of those directly.

Instead,

the car exposes only a few controls:

- the steering wheel,
- the accelerator,
- the brake pedal,
- the gear selector.

Those controls allow you to use the car,

without exposing how the car is built internally.

Software follows the same principle.

---

## The Problem

Imagine a banking application.

Internally,

it might contain thousands of functions.

Some calculate interest.

Some update balances.

Some validate transactions.

Some generate reports.

If another application could call any of those functions directly,

the banking application would lose control over its own behavior.

Instead,

it should expose only the operations that other applications are allowed to use.

Everything else should remain private.

---

## API

An **API (Application Programming Interface)** is the public interface that one piece of software exposes for other software to use.

It defines:

- what operations are available,
- what information those operations require,
- and what results they produce.

The implementation remains hidden.

Conceptually,

it looks like this.

```text
Application
        │
        │ Internal Implementation
        │
──────────────────────────────
        │
     Public Interface
       (API)
        │
Other Applications
```

Applications interact with the API,

not with the application's internal implementation.

---

## Why APIs Are Useful

An API creates a clear boundary.

```text
Inside The Application
        ↓
Implementation

------------------------

Outside The Application
        ↓
API
```

Because of this boundary,

the application can improve,

optimize,

or completely rewrite its internal implementation,

without affecting the applications that use its API.

As long as the API behaves the same,

its users don't need to know what changed internally.

---

## APIs Are Everywhere

Once you understand the idea,

you'll start seeing APIs throughout software.

For example,

the Kotlin Standard Library exposes APIs.

The Android SDK exposes APIs.

Databases expose APIs.

Operating systems expose APIs.

Servers expose APIs.

The technology changes.

The mental model stays exactly the same.

---

## Connecting It To Android

Consider this code.

```kotlin
val text = editText.text.toString()
```

You don't know how `EditText` stores its text internally.

You don't need to.

The Android SDK exposes:

```kotlin
text
```

as part of its public API.

Similarly,

when your application interacts with a server,

it communicates only with the operations the server chooses to expose.

The underlying implementation remains hidden.

---

## The Bigger Picture

An API separates two responsibilities.

```text
API User
        ↓
What Can Be Done

------------------------

API Owner
        ↓
How It Is Done
```

The user focuses on the available operations.

The owner decides how those operations are implemented.

---

## Production Recognition

Whenever you hear developers talk about:

- Android APIs,
- Kotlin APIs,
- Firebase APIs,
- Google Maps APIs,

don't focus on the technology.

Instead think:

> "This is the public interface another piece of software exposes."

That's the common idea behind every API.

---

## Revision

### Mental Model

```text
Application

↓

Public Interface (API)

↓

Other Applications
```

### Remember

An API is the public interface that software exposes to other software.

It defines **what** can be done,

while hiding **how** it is implemented.

---

## One Remaining Question

We've learned that software can expose a public interface called an API.

A natural question appears.

Some APIs,

like the Android SDK,

are available directly inside our application.

Others belong to software running on completely different computers.

How can those APIs be made available over the internet?

That leads us to the next concept.

```text
Web Service
```
