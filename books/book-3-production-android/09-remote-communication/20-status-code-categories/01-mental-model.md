# Status Code Categories

## Observation

In the previous chapter,

we learned that every HTTP response contains a status code.

The status code tells the client the overall outcome of its request.

A natural question appears.

If HTTP uses status codes,

why aren't they simply numbered:

- 1,
- 2,
- 3,
- 4,
- 5,
- 6...

Instead,

we see values like:

- 200
- 201
- 404
- 500

At first,

these numbers seem completely arbitrary.

They're not.

---

## A Thought Experiment

Imagine a hospital emergency department.

Patients are often assigned categories such as:

- Critical
- Serious
- Stable

Before the doctor knows every detail,

they already know the general situation.

The category immediately provides context.

HTTP status codes work the same way.

Before the client knows the exact result,

it first understands the category of the outcome.

---

## Why Categories Exist

Imagine every possible outcome had its own completely unrelated number.

For example,

```text
17

82

143

291

438
```

The client would have to memorize every single value individually.

Instead,

HTTP groups similar outcomes together.

This makes the codes much easier to understand.

---

## The Five Categories

Every HTTP status code belongs to one of five categories.

```text
1xx
    ↓
Informational

------------------------

2xx
    ↓
Success

------------------------

3xx
    ↓
Redirection

------------------------

4xx
    ↓
Client Error

------------------------

5xx
    ↓
Server Error
```

Notice something important.

The first digit already tells you the general outcome.

You don't need to memorize every code to understand what kind of response you're looking at.

---

## An Example

Suppose you receive:

```text
200
```

Even if you've never seen this exact code before,

the first digit tells you:

```text
2xx
    ↓
Success
```

Now suppose you receive:

```text
404
```

Again,

before knowing the exact meaning,

you already know:

```text
4xx
    ↓
Client Error
```

The category immediately gives you useful information.

---

## Connecting It To Android

When your Retrofit request completes,

your application often checks the status code.

Even without looking up the exact number,

developers quickly recognize patterns such as:

- 2xx → Success
- 4xx → Something is wrong with the request
- 5xx → Something went wrong on the server

This helps them decide how the application should respond.

---

## The Bigger Picture

HTTP status codes aren't random numbers.

They're organized into categories.

```text
Status Code
        ↓
Category
        ↓
General Meaning
```

The category communicates the overall outcome.

The specific status code communicates the precise reason.

---

## Production Recognition

Whenever you see:

```kotlin
response.code()
```

don't immediately think:

> "I need to remember exactly what this number means."

Instead,

first identify its category.

The first digit already tells you a great deal about what happened.

---

## Revision

### Mental Model

```text
Status Code
        ↓
Category
        ↓
General Meaning
```

### Remember

The first digit of an HTTP status code tells you its category.

The remaining digits provide a more specific explanation.

---

## One Remaining Question

We've learned that status codes are grouped into categories.

A natural question appears.

The most common category developers encounter is:

```text
2xx
```

What does a successful HTTP response actually mean?

That leads us to the next concept.

```text
2xx Success
```
