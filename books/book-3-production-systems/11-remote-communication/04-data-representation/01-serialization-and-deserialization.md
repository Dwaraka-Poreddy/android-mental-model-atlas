# Serialization & Deserialization

## Observation

In the previous part,

we learned how Android applications communicate with remote Web Services.

A natural question appears.

Imagine your Android application contains the following object.

```kotlin
data class User(
    val id: Int,
    val name: String,
    val email: String
)
```

Can this Kotlin object be sent directly to a server?

The answer is no.

The server might be written in:

- Java
- Python
- Go
- Node.js
- Rust

It has no idea what a Kotlin object is.

So how can two completely different programs exchange the same information?

This is the problem Serialization and Deserialization solve.

---

## A Thought Experiment

Imagine you want to send your bicycle to another country.

You can't simply ride it across the ocean.

Instead,

you first take it apart,

pack it into boxes,

and ship it.

Once it reaches its destination,

it is unpacked,

reassembled,

and becomes a bicycle again.

```text
Bicycle

↓

Disassemble

↓

Pack

↓

Ship

↓

Unpack

↓

Reassemble

↓

Bicycle
```

Objects work in a very similar way.

They are not sent across the network in their original form.

Instead,

they are converted into a representation suitable for transport,

sent,

and reconstructed on the other side.

---

## Serialization

Objects exist only inside a program's memory.

Another computer cannot directly understand them.

Before an object can be stored or transmitted,

it must first be converted into a transferable representation.

This process is called **Serialization**.

```text
Object

↓

Serialization

↓

Transferable Representation
```

After serialization,

the data is no longer tied to a particular programming language.

It can now be sent across a network,

saved to a file,

cached,

or stored in a database.

---

## Deserialization

Once the transferable representation reaches its destination,

it needs to become a usable object again.

This process is called **Deserialization**.

```text
Transferable Representation

↓

Deserialization

↓

Object
```

Serialization and Deserialization always work together.

```text
Object

↓

Serialization

↓

Transferable Representation

↓

Network

↓

Transferable Representation

↓

Deserialization

↓

Object
```

---

## Why This Matters

Serialization allows completely different technologies to communicate.

For example,

```text
Android App

(Kotlin Object)

↓

Serialization

↓

Transferable Representation

↓

Network

↓

Transferable Representation

↓

Deserialization

↓

Python Object

↓

Python Server
```

Neither side needs to understand the other's programming language.

They only need to agree on the representation being exchanged.

This idea isn't limited to networking.

Serialization is also used when applications:

- save files,
- cache data,
- write to databases,
- exchange data between processes.

Networking is simply one of its most common uses.

---

## Production Recognition

Whenever your Android application sends data to a backend,

serialization happens before the request is sent.

When the response comes back,

deserialization happens before your code receives usable objects.

Most networking libraries perform these steps automatically,

but understanding them helps explain what those libraries are doing behind the scenes.

---

## Revision

### Mental Model

```text
Object

↓

Serialization

↓

Transferable Representation

↓

Network

↓

Transferable Representation

↓

Deserialization

↓

Object
```

### Remember

Objects do not travel across a network.

Representations do.

Serialization converts objects into transferable representations.

Deserialization converts those representations back into usable objects.

---

## One Remaining Question

We've learned that objects must first be converted into transferable representations.

A natural question appears.

There are many different ways to represent data.

For example:

- JSON
- XML
- Protocol Buffers
- MessagePack

Which representation do Android applications most commonly use,

and how does it become the objects our application works with?

That leads us to the next concept.

```text
JSON & DTO
```
