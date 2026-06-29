# JSON

## Observation

In the previous chapter,

we learned that objects cannot be sent directly across a network.

Instead,

they are serialized into a transferable representation.

A natural question appears.

What does that representation actually look like?

There are many possible representations,

including:

- JSON
- XML
- Protocol Buffers
- MessagePack

Among these,

JSON has become the most widely used format for exchanging data between applications.

---

## The Problem

Imagine an Android application written in Kotlin.

The backend is written in Python.

Another service is written in Go.

Another is written in Node.js.

They all need to exchange the same information.

But none of them understand each other's objects.

Instead,

they need a common representation that every programming language can understand.

This is where JSON comes in.

---

## JSON

**JSON (JavaScript Object Notation)** is a language-independent format for representing structured data.

Conceptually,

it looks like this.

```text
Kotlin Object

↓

JSON

↓

Network

↓

JSON

↓

Python Object
```

JSON acts as a common language between different systems.

Each application converts its own objects into JSON before sending them,

and reconstructs its own objects after receiving it.

---

## Example

Suppose we have the following Kotlin object.

```kotlin
User(
    id = 1,
    name = "Alice",
    email = "alice@example.com"
)
```

After serialization,

it may look like this.

```json
{
  "id": 1,
  "name": "Alice",
  "email": "alice@example.com"
}
```

Notice that the information remains the same.

Only its representation has changed.

---

## Why JSON Became Popular

JSON became the most widely used data format because it is:

- Human-readable
- Lightweight
- Easy to parse
- Supported by almost every programming language

This allows applications written in completely different technologies to exchange data reliably.

---

## JSON Isn't An Object

One common misconception is that JSON and objects are the same thing.

They are not.

```text
Object

↓

Lives In Memory

------------------------

JSON

↓

Text Representation
```

Objects are used by programs.

JSON is used for communication.

This distinction is one of the most important ideas to remember.

---

## Production Recognition

As an Android developer,

you'll encounter JSON almost everywhere.

For example:

- API documentation
- Postman
- Browser Network tabs
- Server responses
- Application logs

Whenever your application communicates with a backend,

JSON is often the format carrying the data.

---

## The Bigger Picture

JSON isn't the only way to represent structured data.

Other formats include:

```text
Structured Data Formats

├── JSON      ← Most common
├── XML
├── Protocol Buffers
└── MessagePack
```

This Atlas focuses on JSON because it is by far the format Android developers encounter most frequently in production applications.

---

## Revision

### Mental Model

```text
Object

↓

JSON

↓

Network

↓

JSON

↓

Object
```

### Remember

JSON is a language-independent representation of structured data.

Objects are for programming.

JSON is for communication.

---

## One Remaining Question

We've learned that JSON is a common language for communication between applications.

A natural question appears.

When an Android application receives JSON from a server,

our code doesn't manually read each field one by one.

Instead,

we somehow end up with normal Kotlin objects that we can use throughout the application.

How does that transformation happen?

That leads us to the next concept.

```text
DTO
```
