# Remote Data Source

## Observation

We've organized our backend APIs into focused service interfaces.

For example,

```text
UserApi

OrderApi

PaymentApi
```

A natural question appears.

If `UserApi` already exposes everything related to users,

why do production Android applications introduce another layer such as:

```text
UserRemoteDataSource
```

Why not simply inject `UserApi` wherever it's needed?

At first,

creating another layer seems unnecessary.

There must be a reason production applications do this.

---

## A Thought Experiment

Imagine visiting a hospital.

Patients don't directly walk into specialist departments.

Instead,

they first visit the reception desk.

The receptionist:

- checks the appointment,
- identifies the correct department,
- prepares the necessary paperwork,
- and directs the patient appropriately.

Only then does the patient meet the specialist.

The specialist focuses on medical care,

not administrative work.

Production Android applications follow a very similar idea.

The API service knows how to communicate with the backend.

The Remote Data Source knows how the application should interact with those APIs.

Each layer focuses on one responsibility.

---

## The Problem

Without a dedicated layer,

every part of the application that needs remote data communicates directly with API services.

Conceptually,

it looks like this.

```text
Screen A

↓

UserApi

---------------

Screen B

↓

UserApi

---------------

Screen C

↓

UserApi
```

As the application grows,

knowledge about remote communication becomes scattered throughout the codebase.

Changing how remote data is retrieved becomes increasingly difficult.

---

## Remote Data Source

A **Remote Data Source** is the application's dedicated entry point for retrieving and updating remote data.

Instead of allowing different parts of the application to communicate directly with API services,

all remote operations pass through the Remote Data Source.

---

## One Place For Remote Communication

Conceptually,

the application now communicates like this.

```text
Application

↓

UserRemoteDataSource

↓

UserApi

↓

Server
```

Instead of multiple parts of the application knowing about `UserApi`,

only the Remote Data Source communicates with it.

This creates a single place responsible for remote interactions.

---

## Why This Becomes Valuable

At first,

a Remote Data Source may appear to simply forward API calls.

For small applications,

that may even be true.

However,

production applications grow.

Over time,

the Remote Data Source may become responsible for:

- coordinating multiple API calls,
- preparing requests,
- combining responses,
- hiding networking implementation details,
- and centralizing remote communication.

Keeping these responsibilities in one place prevents them from being duplicated throughout the application.

---

## Growing Beyond A Single API Call

Not every operation corresponds to one backend endpoint.

Sometimes,

retrieving one piece of information requires multiple API calls.

Conceptually,

it might look like this.

```text
Application

↓

UserRemoteDataSource

↓

UserApi

+

PreferenceApi

↓

Combined Result
```

The rest of the application doesn't need to know how the information was obtained.

It simply asks the Remote Data Source for the data it needs.

---

## Connecting Everything Together

The networking pipeline now looks like this.

```text
Application

↓

Remote Data Source

↓

API Service Layer

↓

Retrofit

↓

OkHttp

↓

Server
```

Each layer has a single responsibility.

The API Service Layer describes backend endpoints.

The Remote Data Source owns interactions with those endpoints.

---

## Revision

### Mental Model

```text
Application

↓

Remote Data Source

↓

API Service Layer

↓

Server
```

### Remember

A Remote Data Source provides a single place responsible for retrieving and updating remote data.

Instead of allowing remote communication to be scattered throughout the application,

it centralizes all interactions with backend APIs.

---

## Looking Ahead

At this point,

you may naturally wonder:

"If the Remote Data Source is responsible for remote data,

who actually uses it?"

In production Android applications,

that responsibility is commonly handled by the **Repository Pattern**.

The Repository coordinates different data sources,

such as remote services,

local databases,

and caches.

Because the Repository is an architectural concept rather than a networking concept,

we'll explore it in a future Atlas book dedicated to Android Architecture.

---

## One Remaining Question

We've introduced a dedicated layer for working with remote data.

A natural question appears.

Network requests can fail for many different reasons.

How should production applications represent those failures in a clean,

consistent,

and maintainable way?

That leads us to the next concept.

```text
Network Error Types
```
