# GET

## Observation

In the previous chapter,

we learned that every HTTP request includes a method.

The method tells the server what kind of operation the client wants to perform.

A natural question appears.

Suppose the client simply wants to read some information.

What HTTP method should it use?

---

## A Thought Experiment

Imagine you walk into a library.

You ask the librarian:

> "Can I borrow the book 'Atomic Habits'?"

You're not asking the librarian to:

- create a new book,
- edit an existing book,
- remove a book.

You simply want to retrieve information that already exists.

Computers have the same requirement.

Very often,

a client simply wants to read existing information from a server.

---

## GET

HTTP solves this using the **GET** method.

A **GET** request tells the server:

> "Please send me the requested information."

Nothing more.

Nothing less.

Its purpose is simply to retrieve existing data.

---

## Some Examples

A weather application might use GET to retrieve today's forecast.

A shopping application might use GET to retrieve a list of products.

A banking application might use GET to retrieve an account balance.

In every case,

the client is asking the server to return information that already exists.

---

## Connecting It To Android

Whenever you see Retrofit code like:

```kotlin
@GET("/users")
suspend fun getUsers(): List<User>

@GET("/products")
suspend fun getProducts(): List<Product>
```

your application is sending an HTTP GET request.

The annotation isn't requesting that something be created or updated.

It simply tells the server:

> "Return this information."

---

## The Bigger Picture

GET has one responsibility.

```text
Need Existing Information
        ↓
GET
        ↓
Server Returns Data
```

It is the standard HTTP method for retrieving data from a server.

---

## Production Recognition

Whenever you see:

```kotlin
@GET

userApi.getUser()

productApi.getProducts()
```

don't think:

> "This is just fetching data."

Instead think:

> "The client is asking the server to return information that already exists."

The method communicates the client's intention.

---

## Revision

### Mental Model

```text
Need Existing Information
        ↓
GET
        ↓
Server Returns Data
```

### Remember

GET is used when the client wants to retrieve existing information from the server.

It is not intended to create,

modify,

or delete data.

---

## One Remaining Question

We've learned how a client retrieves existing information.

A natural question appears.

What if the client wants to create something new,

such as:

- a new account,
- a new order,
- or a new message?

That leads us to the next concept.

```text
POST
```
