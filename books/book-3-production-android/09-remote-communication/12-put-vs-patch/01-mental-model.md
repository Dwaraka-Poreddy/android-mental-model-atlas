# PUT vs PATCH

## Observation

In the previous chapters,

we learned:

- GET retrieves existing information.
- POST creates new information.

A natural question appears.

What if the information already exists,

and the client simply wants to change it?

Imagine updating:

- your profile picture,
- your delivery address,
- your phone number.

Should HTTP use the same method for every kind of update?

---

## A Thought Experiment

Imagine you're filling out a customer information form.

The company already has your information on file.

One day,

you move to a new house.

Only your address has changed.

Would you fill out the entire form again,

including:

- your name,
- your email,
- your phone number,
- your date of birth,

just to change your address?

Probably not.

You'd simply tell them:

> "Please update my address."

Now imagine a different situation.

The company asks you to submit an entirely new registration form,

replacing every detail they currently have.

These are two different kinds of updates.

Computers face exactly the same distinction.

---

## Two Ways To Update

Sometimes,

the client wants to replace the entire resource.

Sometimes,

it wants to change only a few fields.

HTTP represents these intentions using two different methods.

- **PUT**
- **PATCH**

---

## PUT

A **PUT** request says:

> "Replace the existing resource with this new version."

Think of it as replacing an entire document.

The old version is discarded.

The new version becomes the current one.

Conceptually,

it looks like this.

```text
Existing Resource
        ↓
Replace Everything
        ↓
New Resource
```

---

## PATCH

A **PATCH** request says:

> "Change only these specific fields."

Instead of replacing the whole resource,

the server updates only the parts mentioned in the request.

Conceptually,

it looks like this.

```text
Existing Resource
        ↓
Update Selected Fields
        ↓
Updated Resource
```

---

## An Example

Imagine the server stores:

```text
Name: John
Age: 25
City: London
```

A PUT request might replace it with:

```text
Name: John
Age: 26
City: Paris
```

The server treats this as a complete replacement.

A PATCH request,

however,

might simply say:

```text
City → Paris
```

Only that field changes.

Everything else remains untouched.

---

## Connecting It To Android

In Retrofit,

you'll often see:

```kotlin
@PUT("/users/{id}")

@PATCH("/users/{id}")
```

Although both update existing data,

they communicate different intentions.

PUT asks the server to replace the resource.

PATCH asks the server to modify only specific parts of it.

Exactly how the server behaves depends on its API design,

but these are the standard HTTP meanings.

---

## The Bigger Picture

Both methods update existing information.

The difference is the scope of the update.

```text
Replace Everything
        ↓
PUT
```

```text
Update Specific Fields
        ↓
PATCH
```

Choosing between them is really about expressing intent.

---

## Production Recognition

Whenever you see:

```kotlin
@PUT

@PATCH
```

don't think:

> "They're both update operations."

Instead think:

- **PUT** → Replace the resource.
- **PATCH** → Modify part of the resource.

The method communicates how the client expects the update to happen.

---

## Revision

### Mental Model

```text
Replace Entire Resource
        ↓
PUT
```

```text
Update Specific Fields
        ↓
PATCH
```

### Remember

PUT and PATCH both update existing resources.

The difference is simple.

PUT replaces.

PATCH modifies.

---

## One Remaining Question

We've learned how a client can:

- retrieve information,
- create new information,
- update existing information.

A natural question appears.

What if the client wants to remove information that is no longer needed?

That leads us to the next concept.

```text
DELETE
```
