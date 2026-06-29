# API Versioning & Backward Compatibility

## Observation

In the previous chapters,

we learned how applications exchange structured data.

Objects are serialized,

represented as JSON,

and reconstructed as DTOs inside our application.

A natural question appears.

Imagine you've released version **1.0** of your Android application.

Thousands of users install it.

A month later,

the backend team changes the API.

What happens to all those users who haven't updated the app yet?

This is the problem API Versioning and Backward Compatibility solve.

---

## A Thought Experiment

Imagine your friend has been sending letters to your home for years.

Every letter follows the same address format.

```text
House Number

Street

City

PIN Code
```

One day,

the postal service decides every address should also include the district.

New letters start looking like this.

```text
House Number

Street

City

District

PIN Code
```

The postal service can't simply reject every older letter.

Millions of people are still using the old format.

For a period of time,

both formats need to work.

Software faces the same challenge.

Applications evolve,

but older versions continue to exist.

---

## The Problem

Imagine today's API returns:

```json
{
  "id": 42,
  "name": "Alice"
}
```

Everything works.

Now imagine the backend changes it to:

```json
{
  "id": 42,
  "fullName": "Alice"
}
```

The information is still the same.

Only the contract has changed.

Unfortunately,

older versions of the application still expect a field called `name`.

Without careful planning,

those applications may stop working.

As software evolves,

changes like these become inevitable.

The challenge is introducing new functionality without unexpectedly breaking existing clients.

---

## API Versioning

**API Versioning** is the practice of evolving an API while continuing to support existing clients.

Instead of replacing an API overnight,

a newer version is introduced alongside the older one.

Conceptually,

it looks like this.

```text
Application v1

↓

API v1

------------------------

Application v2

↓

API v2
```

This allows applications to migrate gradually instead of forcing every client to update immediately.

There are many ways to version an API.

Some common approaches include:

```text
URL

/v1/users

------------------------

Header

API-Version: 2

------------------------

Query Parameter

/users?version=2
```

The exact strategy depends on the system,

but the goal remains the same:

allow applications and servers to evolve safely.

---

## Backward Compatibility

Versioning introduces new APIs.

**Backward Compatibility** determines how well older clients continue working with newer servers.

The ideal situation is:

```text
Old Application

↓

New Server

↓

Still Works
```

Whenever possible,

changes should avoid breaking applications that have already been released.

This becomes especially important for mobile applications,

where users may continue using older versions for weeks or even months.

---

## Good And Risky Changes

Not every API change has the same impact.

For example,

adding a new optional field is usually safe.

```json
{
  "id": 42,
  "name": "Alice",
  "profilePhoto": "..."
}
```

Older applications simply ignore the new field.

On the other hand,

renaming or removing an existing field is much riskier.

```text
name

↓

fullName
```

Older applications expecting `name` may no longer function correctly.

Understanding the difference between safe and risky changes helps teams evolve APIs without disrupting users.

---

## Production Recognition

When exploring API documentation,

you'll often encounter terms such as:

```text
/v1

/v2

API-Version
```

These aren't just version numbers.

They represent deliberate strategies for evolving software while continuing to support existing applications.

---

## Looking Back

Over the last few chapters,

we've followed the complete journey of data between systems.

```text
Object

↓

Serialization

↓

JSON

↓

DTO

↓

Application
```

We also learned that this journey doesn't remain fixed forever.

APIs evolve.

Applications evolve.

Good software engineering allows both to change without constantly breaking each other.

---

## Revision

### Mental Model

```text
Application

↓

API Contract

↓

Changes Over Time

↓

Versioning

↓

Backward Compatibility
```

### Remember

API Versioning is a strategy for evolving APIs safely.

Backward Compatibility is the ability of older clients to continue working as those APIs evolve.

Together,

they help independent systems change over time without unnecessary breakage.

---

## One Remaining Question

We've now built a complete conceptual understanding of how Android applications communicate with remote servers.

A natural question appears.

So far,

we've discussed networking only from a conceptual perspective.

How does an Android application actually perform an HTTP request?

Do we manually open sockets,

construct HTTP messages,

and parse JSON ourselves?

Or are there tools that handle these responsibilities for us?

That leads us to the next part.

```text
Android Networking
```
