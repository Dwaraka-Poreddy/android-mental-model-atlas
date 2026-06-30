# API Service Layer

## Observation

We've learned how Retrofit allows us to describe backend APIs using interfaces.

A natural question appears.

Suppose our backend provides APIs for:

- authentication,
- users,
- payments,
- orders,
- notifications,
- and many other features.

One possible approach is to place every endpoint inside a single interface.

```kotlin
interface BackendApi {

    suspend fun login()

    suspend fun logout()

    suspend fun getProfile()

    suspend fun getOrders()

    suspend fun createOrder()

    suspend fun makePayment()

    suspend fun getNotifications()

    // ...
}
```

Technically,

this works.

But as the application grows,

this interface continues to grow as well.

Finding endpoints becomes harder.

Related APIs become scattered.

Maintaining the interface becomes increasingly difficult.

There must be a better way to organize backend APIs.

---

## A Thought Experiment

Imagine a large hospital.

Every patient visits the same hospital,

but there isn't one doctor responsible for every medical problem.

Instead,

the hospital is divided into departments.

```text
Hospital

↓

Cardiology

Neurology

Orthopedics

Pediatrics
```

Each department focuses on one area of expertise.

Patients know exactly where to go.

Doctors only need to understand the patients relevant to their department.

The hospital remains organized,

even as it grows.

Production Android applications organize backend APIs in a very similar way.

Instead of placing every endpoint inside one massive interface,

related endpoints are grouped into separate API services.

---

## The Problem

Without organization,

every backend endpoint eventually ends up inside the same interface.

Conceptually,

it looks like this.

```text
BackendApi

↓

100+

Endpoints
```

As more features are added,

the interface becomes increasingly difficult to navigate,

understand,

and maintain.

---

## API Service Layer

An **API Service Layer** organizes related backend endpoints into focused API interfaces.

Instead of creating one enormous interface,

production applications split endpoints according to their responsibility.

For example,

```text
AuthApi

UserApi

OrderApi

PaymentApi

NotificationApi
```

Each interface represents one area of the backend.

---

## Organizing By Responsibility

Each API service should represent a single backend capability.

For example,

```text
UserApi

↓

User Profile

User Settings

User Preferences
```

---

```text
OrderApi

↓

Orders

Order History

Order Details
```

---

```text
PaymentApi

↓

Payments

Cards

Transactions
```

Notice that each interface remains focused.

Instead of asking,

> "Which interface has this endpoint?"

the answer becomes obvious from the feature itself.

---

## Why This Scales Better

As applications grow,

new backend capabilities simply introduce new API services.

```text
API Service Layer

├── AuthApi

├── UserApi

├── OrderApi

├── PaymentApi

├── NotificationApi

└── InventoryApi
```

Existing interfaces remain small,

focused,

and easy to understand.

---

## Connecting Everything Together

Conceptually,

the networking stack now looks like this.

```text
Application

↓

API Service Layer

├── AuthApi

├── UserApi

├── OrderApi

├── PaymentApi

↓

Retrofit

↓

OkHttp

↓

Server
```

The API Service Layer doesn't perform network requests itself.

Instead,

it provides an organized way to describe everything the backend can do.

---

## Revision

### Mental Model

```text
Application

↓

API Service Layer

├── AuthApi

├── UserApi

├── OrderApi

├── PaymentApi

↓

Retrofit

↓

Server
```

### Remember

An API Service Layer organizes backend endpoints into small,

focused API interfaces.

As applications grow,

this organization keeps networking code easier to navigate,

understand,

and maintain.

---

## One Remaining Question

We've organized our backend APIs into focused service interfaces.

A natural question appears.

Who should actually call these API services?

Should every screen communicate with them directly?

Or should another layer be responsible for interacting with the network?

That leads us to the next concept.

```text
Remote Data Source
```
