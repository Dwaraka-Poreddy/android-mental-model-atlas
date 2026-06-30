# Error Handling

## Observation

We've now built a complete networking pipeline.

Our application can:

- describe APIs,
- convert JSON,
- customize requests,
- configure timeouts,
- communicate with remote servers.

Most of the time,

everything works as expected.

A natural question appears.

What happens when something goes wrong?

The device might not have an internet connection.

The server might be unavailable.

The request might time out.

The server might reject the request.

The application might even receive invalid data.

Should every failure be handled in exactly the same way?

Clearly not.

There must be a better approach.

---

## A Thought Experiment

Imagine ordering a package online.

Most deliveries arrive successfully.

Sometimes,

however,

things go wrong.

The address might be incorrect.

The recipient may not be available.

The delivery truck could break down.

The warehouse might temporarily stop processing orders.

Although the outcome is the same—

the package wasn't delivered—

the reason is completely different.

And because the reason is different,

the solution is also different.

- Correct the address.
- Try delivering tomorrow.
- Send another vehicle.
- Wait until the warehouse is available again.

Networking works in a very similar way.

A request can fail for many different reasons.

Before deciding what to do,

the application must first understand **why** the request failed.

---

## The Problem

Not every failure represents the same situation.

Consider these examples.

```text
No Internet
```

The user may simply need to reconnect.

---

```text
401 Unauthorized
```

The user may need to log in again.

---

```text
500 Internal Server Error
```

The server may be experiencing a temporary problem.

---

```text
Request Timed Out
```

The request took longer than the application was willing to wait.

Although all of these represent failures,

they require completely different responses.

Treating every failure the same would lead to a poor user experience.

---

## Error Handling

**Error Handling** is the process of:

- detecting that a failure occurred,
- understanding why it occurred,
- and choosing an appropriate response.

The important idea isn't simply detecting failures.

The important idea is responding differently depending on the nature of the failure.

---

## Every Failure Is A Decision

Conceptually,

every request eventually reaches one of two outcomes.

```text
Request

↓

Success?

├── Yes
│
│   ↓
│
│ Continue
│
└── No
    │
    ↓
Identify Cause
    │
    ↓
Choose Appropriate Action
```

The application shouldn't ask:

> "Did the request fail?"

Instead,

it should ask:

> "Why did the request fail?"

Only then can it decide the appropriate next step.

---

## Different Failures Need Different Responses

Different failures naturally lead to different actions.

```text
No Internet

↓

Ask the user to reconnect.
```

---

```text
401 Unauthorized

↓

Ask the user to authenticate again.
```

---

```text
500 Internal Server Error

↓

Inform the user that the service is temporarily unavailable.
```

---

```text
Request Timed Out

↓

Decide whether retrying the request makes sense.
```

Notice something important.

The application isn't reacting to failure itself.

It's reacting to the reason behind the failure.

---

## Error Handling Is About Decisions

Many developers initially think error handling is simply writing:

```kotlin
try {
    ...
} catch (...) {
    ...
}
```

But that's only one implementation technique.

Conceptually,

error handling is about making good decisions when something goes wrong.

The mechanism used to detect the failure is less important than deciding the correct response.

---

## Looking Ahead

At this point,

you may naturally have questions such as:

- What exceptions do networking libraries actually throw?
- What's the difference between network failures and HTTP failures?
- When should an application retry a request?
- How do production applications expose failures to the UI?
- How are exceptions converted into user-friendly application states?

These are production architecture questions.

We'll answer them in the next part of the Atlas,

where we'll build a complete production networking architecture.

---

## Revision

### Mental Model

```text
Request

↓

Success?

├── Yes
│
│   ↓
│
│ Continue
│
└── No
    │
    ↓
Identify Cause
    │
    ↓
Choose Appropriate Action
```

### Remember

Error handling isn't simply about detecting failures.

It's about understanding why a request failed,

and responding appropriately based on the nature of that failure.

---

## Looking Back

You've now completed the Android Networking part of the Atlas.

Together,

these concepts describe the complete networking pipeline used by production Android applications.

```text
Application

↓

Retrofit

↓

Converter Factory

↓

OkHttp
│
├── Interceptors
├── Timeouts
│
▼

HTTP

↓

Server
```

You now understand:

- how applications describe APIs,
- how HTTP requests are performed,
- how JSON becomes Kotlin objects,
- how common networking behavior is applied,
- how applications avoid waiting forever,
- and why different failures require different responses.

In the next part,

we'll move from networking technologies to networking architecture,

and learn how production Android applications organize these concepts into clean, maintainable codebases.
