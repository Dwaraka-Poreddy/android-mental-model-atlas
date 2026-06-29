# Production Code

In the previous document,

we learned that a DTO represents data exchanged with another system.

Let's see what this looks like in a real Android application.

---

## A Typical API Response

Imagine the backend returns the following JSON.

```json
{
  "user_id": 42,
  "full_name": "Alice Johnson",
  "email": "alice@example.com"
}
```

Our application doesn't work with this JSON directly.

Instead,

it is converted into a Kotlin object.

---

## A DTO

A typical DTO might look like this.

```kotlin
data class UserDto(
    val userId: Int,
    val fullName: String,
    val email: String
)
```

Notice that the DTO closely resembles the JSON received from the server.

This is intentional.

A DTO's primary responsibility is to represent the API contract.

---

## Why The Names Are Different

Sometimes,

the field names used by the backend don't follow Kotlin naming conventions.

For example,

the backend may send:

```json
{
  "user_id": 42,
  "full_name": "Alice Johnson"
}
```

Inside our application,

we'd prefer property names like:

```kotlin
userId
fullName
```

Serialization libraries allow us to map these automatically.

For example:

```kotlin
data class UserDto(

    @Json(name = "user_id")
    val userId: Int,

    @Json(name = "full_name")
    val fullName: String
)
```

The application continues using clean Kotlin property names,

while still communicating correctly with the backend.

---

## Where Do DTOs Live?

Production Android projects usually keep DTOs together inside the networking layer.

A common folder structure looks like this.

```text
network/

├── dto/
│   ├── UserDto.kt
│   ├── OrderDto.kt
│   ├── RideDto.kt
│   └── PaymentDto.kt
│
├── api/
│
└── service/
```

The exact folder names vary,

but keeping DTOs close to the networking layer is a very common practice.

---

## Recognizing DTOs

When exploring an unfamiliar codebase,

DTOs are usually easy to identify.

Some common naming conventions are:

```text
UserDto
OrderDto
RideDto
PaymentDto
NotificationDto
```

Some projects omit the `Dto` suffix altogether,

but many teams keep it because it clearly communicates the object's purpose.

---

## What Happens Next?

A typical request flow looks like this.

```text
Server

↓

JSON

↓

UserDto

↓

Application
```

At this point,

the DTO has successfully brought data into the application.

What happens after that depends on the architecture used by the project.

Some applications work directly with DTOs.

Others convert them into different models.

We'll explore those architectural decisions later in the Atlas.

For now,

the important takeaway is simply this:

DTOs are the first Kotlin representation of data received from another system.

---

## Production Recognition

As you explore production Android projects,

you'll begin noticing DTOs everywhere.

Whenever you encounter a class whose primary responsibility is representing data exchanged with an API,

you're almost certainly looking at a DTO.

Once you recognize this pattern,

reading networking code becomes significantly easier.
