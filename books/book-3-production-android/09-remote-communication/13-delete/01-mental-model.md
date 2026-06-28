# DELETE

## Observation

In the previous chapters,

we learned how a client can:

- retrieve existing information using GET,
- create new information using POST,
- update existing information using PUT or PATCH.

A natural question appears.

What if the information is no longer needed?

Imagine deleting:

- a shopping cart item,
- a saved address,
- a photo,
- a user account.

The client isn't trying to retrieve,

create,

or update anything.

It simply wants something to be removed.

---

## A Thought Experiment

Imagine you borrow a locker at a gym.

One day,

you decide you no longer need it.

You don't ask the receptionist to:

- show you the locker,
- create a new locker,
- or update the locker.

Instead,

you simply ask:

> "Please remove my locker assignment."

The locker is no longer associated with you.

Computers often need to perform the same kind of operation.

---

## DELETE

HTTP solves this using the **DELETE** method.

A **DELETE** request tells the server:

> "Please remove this resource."

The client's intention is not to modify the resource.

It is to remove it entirely.

---

## Some Examples

A shopping application might use DELETE to remove an item from the cart.

A photo application might use DELETE to remove an image.

A social media application might use DELETE to remove a post.

A banking application might use DELETE to remove a saved beneficiary.

In every case,

the client is asking the server to remove an existing resource.

---

## Connecting It To Android

Whenever you see Retrofit code like:

```kotlin
@DELETE("/users/{id}")
suspend fun deleteUser(
    @Path("id") id: String
)

@DELETE("/cart/{itemId}")
suspend fun removeCartItem(
    @Path("itemId") itemId: String
)
```

your application is sending an HTTP DELETE request.

The client is telling the server that the specified resource should be removed.

---

## The Bigger Picture

DELETE has one primary responsibility.

```text
Need To Remove A Resource
        ↓
DELETE
        ↓
Server Removes It
```

Just like GET,

POST,

and PUT,

DELETE communicates the client's intention before the server processes the request.

---

## Production Recognition

Whenever you see:

```kotlin
@DELETE

userApi.deleteUser()

cartApi.removeItem()
```

don't think:

> "This is just another API call."

Instead think:

> "The client is asking the server to remove an existing resource."

The HTTP method communicates that intention.

---

## Revision

### Mental Model

```text
Need To Remove A Resource
        ↓
DELETE
        ↓
Server Removes It
```

### Remember

DELETE is used when the client wants an existing resource to be removed from the server.

It completes the set of common CRUD operations supported by HTTP.

---

## One Remaining Question

We've now learned the most common HTTP methods.

A natural question appears.

When the client sends a request,

how does it specify **which** user,

**which** product,

or **which** order it wants to operate on?

That leads us to the next concept.

```text
URL
```
