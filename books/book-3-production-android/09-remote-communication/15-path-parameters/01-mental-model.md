# Path Parameters

## Observation

In the previous chapter,

we learned that a URL identifies the resource a client wants to interact with.

A natural question appears.

Sometimes a URL looks like:

```text
/users
```

Other times it looks like:

```text
/users/123
```

What does that extra value represent?

---

## A Thought Experiment

Imagine visiting a hotel.

You ask the receptionist:

> "I'd like to visit Room 305."

The hotel already knows what a room is.

What you provide is the room number that identifies the specific room you want.

Without that number,

the receptionist wouldn't know which room you're referring to.

Computers face the same problem.

Sometimes,

it's not enough to identify a type of resource.

The client needs to identify one specific resource.

---

## Identifying A Specific Resource

Consider these two URLs.

```text
/users
```

```text
/users/123
```

The first refers to the collection of users.

The second refers to one particular user.

The value:

```text
123
```

identifies the specific resource.

That value is called a **Path Parameter**.

---

## Path Parameters

A **Path Parameter** is a value embedded inside the URL path that uniquely identifies a specific resource.

Conceptually,

it looks like this.

```text
/users/{id}
```

When the client sends:

```text
/users/123
```

the server understands:

> "The client is referring to the user whose ID is 123."

---

## More Examples

```text
/products/42
```

refers to one specific product.

```text
/orders/987
```

refers to one specific order.

```text
/messages/15
```

refers to one specific message.

The pattern is always the same.

The path parameter identifies exactly which resource the client wants.

---

## Connecting It To Android

In Retrofit,

path parameters are represented using placeholders.

```kotlin
@GET("/users/{id}")
suspend fun getUser(
    @Path("id") id: String
): User
```

If the client passes:

```kotlin
id = "123"
```

Retrofit constructs the URL:

```text
/users/123
```

The placeholder is replaced with the actual value before the request is sent.

---

## The Bigger Picture

The URL identifies the resource.

A path parameter identifies the specific resource.

```text
URL
      ↓
Resource
      ↓
Path Parameter
      ↓
Specific Resource
```

Together,

they tell the server exactly what the client wants to operate on.

---

## Production Recognition

Whenever you see:

```kotlin
@Path("id")

@Path("userId")

@Path("productId")
```

don't think:

> "This is just another Retrofit annotation."

Instead think:

> "This value identifies the exact resource the client wants."

The path parameter becomes part of the URL.

---

## Revision

### Mental Model

```text
URL
      ↓
Resource
      ↓
Path Parameter
      ↓
Specific Resource
```

### Remember

A path parameter identifies one specific resource.

It becomes part of the URL itself.

---

## One Remaining Question

We've learned how to identify a specific resource.

A natural question appears.

What if the client doesn't want just one resource?

What if it wants to:

- search,
- filter,
- sort,
- or limit a collection of resources?

That leads us to the next concept.

```text
Query Parameters
```
