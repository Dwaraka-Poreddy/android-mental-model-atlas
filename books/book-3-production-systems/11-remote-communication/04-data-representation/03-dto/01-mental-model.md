# DTO (Data Transfer Object)

## Observation

In the previous chapter,

we learned that applications exchange information using JSON.

We also discovered that our Android application doesn't usually work with raw JSON.

Instead,

our code works with Kotlin objects.

A natural question appears.

How does a JSON response become the Kotlin objects our application actually uses?

This is the problem DTOs solve.

---

## A Thought Experiment

Imagine a company that regularly receives packages from different suppliers.

Each supplier labels and packages items in its own way.

Before those packages reach the warehouse,

they first pass through a receiving desk.

The receiving desk doesn't use the products.

Instead,

it:

- receives them,
- checks them,
- labels them,
- and prepares them for the company's internal systems.

```text
Supplier

↓

Package

↓

Receiving Desk

↓

Company Inventory
```

The receiving desk acts as a translator between the outside world and the company's internal way of working.

DTOs play a very similar role.

They receive data from external systems,

prepare it,

and hand it over to the rest of the application.

---

## What Is A DTO?

**DTO** stands for **Data Transfer Object**.

A DTO is an object whose only responsibility is to represent data being transferred between systems.

For example,

a server may send the following JSON.

```json
{
  "user_id": 42,
  "full_name": "Alice Johnson",
  "created_at": "2026-06-29T12:30:00Z"
}
```

After deserialization,

our application might create a DTO like this.

```kotlin
data class UserDto(
    val userId: Int,
    val fullName: String,
    val createdAt: String
)
```

Notice that the DTO closely matches the data received from the server.

Its job is to represent the API contract,

not the application's business logic.

---

## Mapping

A common flow inside Android applications looks like this.

```text
JSON

↓

UserDto

↓

User

↓

UI
```

The DTO receives data from the server.

It is then converted into the application's own model.

This process is called **mapping**.

By separating these models,

the rest of the application stays independent of backend-specific details.

---

## Why This Matters

Imagine the backend team decides to rename a field.

```text
full_name

↓

display_name
```

Without DTOs,

this change could affect the entire application.

With DTOs,

only the DTO and its mapping need to change.

```text
Backend

↓

DTO

↓

Domain Model

↓

UI
```

The rest of the application continues working exactly as before.

This separation makes production applications easier to maintain as APIs evolve.

---

## Production Recognition

When you explore a production Android codebase,

you'll often find classes such as:

```kotlin
UserDto
OrderDto
RideDto
PaymentDto
NotificationDto
```

These are not business models.

They are representations of the data exchanged with external systems.

Once you recognize this,

the architecture of production applications becomes much easier to understand.

---

## Revision

### Mental Model

```text
Server

↓

JSON

↓

DTO

↓

Domain Model

↓

UI
```

### Remember

A DTO represents data exactly as it is exchanged with another system.

It is not the application's business model.

Its primary responsibility is to isolate the rest of the application from changes in external systems.

---

## One Remaining Question

We've learned how JSON becomes Kotlin objects,

and why production applications introduce DTOs as a boundary between the backend and the rest of the application.

A natural question appears.

Applications continue evolving long after they are released.

New fields are added.

Existing fields are renamed.

Some fields disappear completely.

How can clients and servers continue communicating without breaking each other every time an API changes?

That leads us to the next concept.

```text
API Versioning & Backward Compatibility
```
