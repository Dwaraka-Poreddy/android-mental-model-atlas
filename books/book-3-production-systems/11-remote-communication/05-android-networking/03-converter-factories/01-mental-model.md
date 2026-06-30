# Converter Factories

## Observation

In the previous chapter,

we learned that Retrofit allows us to describe APIs as Kotlin functions.

For example,

```kotlin
val users = userApi.getUsers()
```

looks like an ordinary Kotlin function call.

A natural question appears.

The server actually responded with JSON.

So how did this function return:

```kotlin
List<UserDto>
```

instead of raw JSON?

Something must have converted the JSON into Kotlin objects.

---

## A Thought Experiment

Imagine two people speaking different languages.

One speaks English.

The other speaks Japanese.

Neither understands the other's language.

To communicate,

they rely on a translator.

```text
English

↓

Translator

↓

Japanese
```

The translator doesn't change the meaning of the conversation.

It simply converts one representation into another.

Converter Factories play a very similar role.

They translate between:

```text
JSON

↓

Kotlin Objects
```

and

```text
Kotlin Objects

↓

JSON
```

---

## The Problem

Retrofit knows how to describe APIs.

OkHttp knows how to perform HTTP communication.

But neither of them understands JSON.

Imagine the server responds with:

```json
{
    "id": 1,
    "name": "Alice"
}
```

Your application, however, wants to work with:

```kotlin
UserDto(
    id = 1,
    name = "Alice"
)
```

Someone has to perform this conversion.

---

## Converter Factory

A **Converter Factory** is responsible for translating data between HTTP representations and Kotlin objects.

Conceptually,

it sits between Retrofit and your application.

```text
Application

↓

Retrofit

↓

Converter Factory

↓

JSON

↓

OkHttp

↓

HTTP

↓

Server
```

Whenever data travels between your application and the server,

the Converter Factory performs the translation.

---

## Conversion Happens Both Ways

Converter Factories don't only process responses.

They also prepare requests.

When receiving data,

they convert:

```text
JSON

↓

UserDto
```

When sending data,

they convert:

```text
CreateUserRequest

↓

JSON
```

The same component handles both directions of communication.

---

## Common Converter Libraries

Retrofit itself doesn't know how to understand JSON.

Instead,

it delegates that responsibility to a converter library.

Some of the most common converter libraries you'll encounter are:

- Gson
- Moshi
- kotlinx.serialization

Although their implementations differ,

their responsibility is exactly the same.

```text
JSON

⇄

Kotlin Objects
```

From Retrofit's perspective,

they are interchangeable.

The choice usually depends on the project's requirements and the team's preferences.

---

## Connecting Everything Together

Our networking stack now looks like this.

```text
Application

↓

Retrofit

↓

Converter Factory

↓

OkHttp

↓

HTTP

↓

Server
```

Each layer has a specific responsibility.

- The application decides **what** it wants.
- Retrofit describes the API.
- The Converter Factory translates data.
- OkHttp performs HTTP communication.
- HTTP defines the communication protocol.

Together,

they allow applications to exchange strongly typed Kotlin objects instead of raw JSON.

---

## Revision

### Mental Model

```text
Application

↓

Retrofit

↓

Converter Factory

↓

OkHttp

↓

HTTP

↓

Server
```

### Remember

A Converter Factory translates between JSON and Kotlin objects.

Retrofit relies on it whenever data needs to move between your application and a remote server.

---

## One Remaining Question

We've now built almost the entire networking pipeline.

A natural question appears.

Suppose every request sent by our application needs to:

- attach an authentication token,
- add common headers,
- log requests and responses,
- or modify requests before they're sent.

Do we write that code inside every API call?

There must be a better way.

That leads us to the next concept.

```text
Interceptors
```
