# Query Parameters

## Observation

In the previous chapter,

we learned that path parameters identify a specific resource.

For example,

```text
/users/123
```

identifies the user whose ID is 123.

A natural question appears.

What if the client doesn't want just one specific resource?

What if it wants to:

- search users,
- filter products,
- sort results,
- limit the number of items returned?

The client isn't identifying a different resource.

It's asking for the same resource in a different way.

---

## A Thought Experiment

Imagine you visit a restaurant.

You ask the waiter:

> "I'd like a pizza."

The waiter understands what resource you want.

But then you add:

- Extra cheese
- No onions
- Thin crust

Notice something important.

You're still ordering the same pizza.

You're simply customizing it.

Computers have the same requirement.

Sometimes,

the client doesn't want a different resource.

It wants the resource returned in a particular way.

---

## Customizing A Request

Consider these URLs.

```text
/products
```

```text
/products?category=electronics
```

```text
/products?category=electronics&sort=price
```

Every request is still targeting:

```text
/products
```

The resource hasn't changed.

The client is simply asking the server to customize the results.

---

## Query Parameters

A **Query Parameter** is additional information attached to a URL that tells the server how the client wants the results returned.

Query parameters commonly describe things like:

- filtering,
- searching,
- sorting,
- pagination.

They don't identify the resource.

They customize the request.

---

## More Examples

```text
/users?name=John
```

Search for users named John.

---

```text
/products?category=electronics
```

Return only products in the Electronics category.

---

```text
/orders?page=2
```

Return the second page of orders.

In every example,

the resource stays the same.

Only the requested result changes.

---

## Path Parameters vs Query Parameters

This is one of the most important distinctions in HTTP.

A path parameter answers:

> "Which resource?"

A query parameter answers:

> "How would you like that resource returned?"

Conceptually,

the difference looks like this.

```text
/users/123
        ↓
Specific Resource
```

```text
/users?active=true
        ↓
Customized Results
```

One identifies.

The other customizes.

---

## Connecting It To Android

Retrofit represents query parameters using the `@Query` annotation.

```kotlin
@GET("/products")
suspend fun getProducts(
    @Query("category") category: String,
    @Query("sort") sort: String
): List<Product>
```

If the client passes:

```kotlin
category = "electronics"
sort = "price"
```

Retrofit constructs:

```text
/products?category=electronics&sort=price
```

The URL identifies the resource.

The query parameters customize the response.

---

## The Bigger Picture

Path parameters and query parameters solve different problems.

```text
Path Parameter
        ↓
Identifies The Resource

------------------------

Query Parameter
        ↓
Customizes The Result
```

Understanding this distinction makes it much easier to read production APIs.

---

## Production Recognition

Whenever you see:

```kotlin
@Query("page")

@Query("sort")

@Query("search")
```

don't think:

> "These are just extra arguments."

Instead think:

> "These values tell the server how I'd like the results returned."

They customize the request without changing the resource itself.

---

## Revision

### Mental Model

```text
Path Parameter
        ↓
Identifies The Resource

------------------------

Query Parameter
        ↓
Customizes The Result
```

### Remember

Path parameters answer:

> "Which resource?"

Query parameters answer:

> "How should that resource be returned?"

---

## One Remaining Question

we learned that the URL identifies the resource.

For example,

```text
/users/123
```

identifies a specific user.

A natural question appears.

Suppose the client also needs to send information such as:

- an authentication token,
- the user's preferred language,
- the application version,
- the type of data it can understand.

Why can't all of this simply be added to the URL?

Suppose we wrote something like this.

```text
/users/123/token/abc123/language/en/version/12
```

Looking at this URL,

it's difficult to answer questions like:

- Which part identifies the resource?
- Which part is authentication?
- Which part is the user's language?
- Which part is just additional information?

The URL starts mixing together information that serves completely different purposes.

HTTP separates these responsibilities instead.

That leads us to the next concept.

```text
Headers
```
