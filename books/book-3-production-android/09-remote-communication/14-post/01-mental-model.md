# POST

## Observation

In the previous chapter,

we learned that GET is used when a client wants to retrieve existing information from a server.

A natural question appears.

What if the information doesn't exist yet?

Suppose a user wants to:

- create a new account,
- place a new order,
- send a new message.

Retrieving existing data isn't enough.

The client needs a way to ask the server to create something new.

---

## A Thought Experiment

Imagine you're filling out a membership form at a gym.

You write down:

- your name,
- your phone number,
- your email address.

You hand the completed form to the receptionist.

The receptionist doesn't simply show you an existing membership.

Instead,

they create a brand new membership using the information you provided.

Computers face the same situation.

Sometimes,

the client isn't asking for existing information.

It's asking the server to create something new.

---

## POST

HTTP solves this using the **POST** method.

A **POST** request tells the server:

> "Here's some information. Please use it to create something new."

Unlike GET,

which retrieves existing data,

POST is commonly used when the client wants the server to perform a creation operation.

---

## Some Examples

A shopping application might use POST to create a new order.

A messaging application might use POST to send a new message.

A social media application might use POST to publish a new post.

A banking application might use POST to create a new payment.

In every case,

the client is asking the server to create or process something new.

---

## Connecting It To Android

Whenever you see Retrofit code like:

```kotlin
@POST("/users")
suspend fun createUser(
    @Body request: CreateUserRequest
): User

@POST("/login")
suspend fun login(
    @Body request: LoginRequest
): LoginResponse
```

your application is sending an HTTP POST request.

Notice that the client provides information to the server.

The server then decides what to do with it.

---

## The Bigger Picture

POST has one primary responsibility.

```text
Need To Create Something New
        ↓
POST
        ↓
Server Creates Or Processes It
```

The client isn't requesting existing information.

It's asking the server to perform a creation operation.

---

## Production Recognition

Whenever you see:

```kotlin
@POST

userApi.createUser()

orderApi.placeOrder()

authApi.login()
```

don't think:

> "This is just another API call."

Instead think:

> "The client is asking the server to create or process something."

The HTTP method communicates that intention.

---

## Revision

### Mental Model

```text
Need To Create Something New
        ↓
POST
        ↓
Server Creates Or Processes It
```

### Remember

POST is typically used when the client wants the server to create a new resource or process newly submitted information.

It is fundamentally different from GET,

which is used to retrieve existing data.

---

## One Remaining Question

We've learned how a client:

- retrieves existing information using GET,
- creates new information using POST.

A natural question appears.

What if the information already exists,

and the client simply wants to change it?

That leads us to the next concept.

```text
PUT vs PATCH
```
