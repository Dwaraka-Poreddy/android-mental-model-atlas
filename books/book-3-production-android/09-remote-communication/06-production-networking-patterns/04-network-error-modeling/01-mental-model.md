# Network Error Modeling

## Observation

In the previous chapter,

we learned that every network request should produce one well-defined result.

Conceptually,

every request ends like this.

```text
Network Result

├── Success

└── Error
```

A natural question appears.

What should the **Error** contain?

One possible approach is to simply expose the exception thrown by the networking library.

After all,

the exception already describes what went wrong.

So why introduce another error model?

There must be a better way.

---

## A Thought Experiment

Imagine visiting a hospital.

During your examination,

dozens of measurements are collected.

```text
Blood Pressure

Blood Sugar

Heart Rate

White Blood Cell Count

...
```

These measurements are useful to doctors.

But patients don't receive a report filled with raw medical values.

Instead,

the doctor interprets those measurements and provides a diagnosis.

```text
Diagnosis

↓

Viral Infection
```

or

```text
Diagnosis

↓

Diabetes
```

The diagnosis hides the low-level medical details.

Patients receive information they can understand and act upon.

Production Android applications work in a very similar way.

Networking libraries produce low-level exceptions.

The application interprets those exceptions and converts them into meaningful application errors.

---

## The Problem

Networking libraries communicate failures using exceptions.

For example,

a request may fail because:

```text
UnknownHostException
```

or

```text
SocketTimeoutException
```

or

```text
HttpException(401)
```

These exceptions are useful for the networking layer.

But should the rest of the application know what a socket is?

Should the UI understand SSL?

Should business logic depend on Retrofit-specific exceptions?

Ideally,

no.

Those are implementation details.

---

## Speaking The Application's Language

Instead of exposing raw exceptions,

production applications create their own language for describing failures.

For example,

instead of exposing:

```text
UnknownHostException
```

the application may represent it as:

```text
No Internet Connection
```

---

Instead of:

```text
SocketTimeoutException
```

the application may represent it as:

```text
Request Timed Out
```

---

Instead of:

```text
HttpException(401)
```

the application may represent it as:

```text
Authentication Required
```

Notice something important.

The application is no longer exposing networking terminology.

It is communicating using concepts that make sense to the rest of the application.

---

## Modeling Errors

Conceptually,

production applications define a set of application-specific errors.

```text
Network Error

├── No Internet

├── Request Timed Out

├── Authentication Required

├── Server Error

└── Unknown Error
```

These errors become the application's common language for describing networking failures.

Every part of the application understands these errors,

regardless of which networking library is used internally.

---

## Translating Between Worlds

Conceptually,

the networking layer performs a translation.

```text
Networking Library

↓

Exception

↓

Application Error

↓

Rest of Application
```

The networking library speaks in terms of exceptions.

The application speaks in terms of business-friendly errors.

This translation separates infrastructure details from application logic.

---

## Why This Scales Better

Imagine exposing raw exceptions throughout the application.

Every screen,

every ViewModel,

and every feature would need to understand:

- networking exceptions,
- HTTP libraries,
- connectivity failures,
- protocol details.

Instead,

only the networking layer understands those concepts.

Everyone else works with a simple,

consistent,

application-specific error model.

---

## Connecting Everything Together

The networking pipeline now looks like this.

```text
Request

↓

Network Result

├── Success
│
└── Error
     │
     ▼

Application Error
```

The application no longer depends directly on the language used by networking libraries.

It communicates using its own error model.

---

## Revision

### Mental Model

```text
Networking Exception

↓

Application Error

↓

Rest of Application
```

### Remember

Production applications don't expose raw networking exceptions directly.

Instead,

they translate low-level infrastructure failures into meaningful,

application-specific errors that the rest of the application can understand.

---

## One Remaining Question

We've learned how production applications model networking failures.

A natural question appears.

Should every modeled error be treated the same way?

Should every failure be retried?

Or do different failures require different retry strategies?

That leads us to the next concept.

```text
Retry Strategies
```
