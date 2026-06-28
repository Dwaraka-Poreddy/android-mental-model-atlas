# Choosing State Ownership

## Observation

Throughout the previous documents,

we've learned about many different ways to store state.

For example:

- `remember`
- `rememberSaveable`
- `MutableState`
- `StateFlow`
- `ViewModel`

A natural question appears.

> **How do I know which one to use?**

Choosing the wrong state container is rarely the real problem.

The real problem is choosing the **wrong owner**.

Before choosing *how* to store state,

we must first decide **who should own it**.

---

## A Production Example

Imagine you're building a login screen.

You need to store:

- Email
- Password
- Password visibility
- Loading state
- Login result
- Snackbar message
- Remember me checkbox

A common first implementation might look like this.

```kotlin
@Composable
fun LoginScreen() {

    var email by remember {
        mutableStateOf("")
    }

    var password by remember {
        mutableStateOf("")
    }

    var passwordVisible by remember {
        mutableStateOf(false)
    }

    var isLoading by remember {
        mutableStateOf(false)
    }

    var loginResult by remember {
        mutableStateOf<LoginResult?>(null)
    }

    var snackbarMessage by remember {
        mutableStateOf<String?>(null)
    }

    ...
}
```

Nothing here is technically wrong.

The screen works.

But pause for a moment.

**Would you keep all of this state inside the composable?**

Before reading further,

take a minute to think about it.

---

## The Wrong Mental Model

Many developers instinctively think:

> **"This composable uses the state, so this composable should own the state."**

That works for small demos.

It usually doesn't scale to production applications.

Using state and owning state are two completely different responsibilities.

This distinction is one of the biggest mindset shifts in Compose architecture.

---

## The Key Insight

Instead of asking:

> **"Where should I store this state?"**

ask:

> **"Who is responsible for this information?"**

Notice how different those questions are.

Ownership isn't determined by:

- where the state is used,
- where it was created,
- or where it's easiest to access.

Ownership is determined by **responsibility**.

---

## Let's Classify The State

Instead of treating all state equally,

let's examine each one individually.

### Email

```kotlin
var email by remember {
    mutableStateOf("")
}
```

Who owns this information?

The user is editing text.

The composable simply displays and updates it.

At first glance,

this feels like UI state.

We'll revisit this decision in the next chapters.

---

### Password Visibility

```kotlin
var passwordVisible by remember {
    mutableStateOf(false)
}
```

Who owns this?

Only this screen cares.

No business logic depends on it.

This is purely visual behavior.

This clearly feels like UI state.

---

### Login Result

```kotlin
var loginResult by remember {
    mutableStateOf<LoginResult?>(null)
}
```

This one feels different.

The composable didn't produce this information.

It comes from the business layer.

Other parts of the UI may also depend on it.

It may need to survive configuration changes.

This doesn't feel like local UI state anymore.

---

### Loading State

```kotlin
var isLoading by remember {
    mutableStateOf(false)
}
```

This is where things become interesting.

Suppose it means:

> "A login request is currently running."

That sounds like business state.

Now suppose it means:

> "The login button is playing a loading animation."

That's purely UI behavior.

Same variable.

Completely different ownership.

This illustrates an important lesson.

You can't determine ownership from the variable name alone.

You must understand **what the state represents**.

---

## The Engineering Principle

Don't classify state by:

- type,
- name,
- or where it's used.

Classify it by **responsibility**.

Ownership should answer one question.

> **Who is responsible for keeping this information correct?**

That question naturally leads you toward the correct owner.

---

## A Better Decision Process

Whenever you introduce new state,

follow this process.

```text
New State

↓

Who Owns It?

↓

How Long Should It Live?

↓

Who Needs To Read It?

↓

Choose The Owner

↓

Choose The State Container
```

Notice something important.

Choosing the state container is the **last** step,

not the first.

---

## Why This Matters

Incorrect ownership often leads to problems like:

- duplicated state
- inconsistent UI
- synchronization bugs
- unnecessary recomposition
- difficult testing
- complicated data flow

Many Compose architecture problems aren't really Compose problems.

They're ownership problems.

---

## Production Recognition

Whenever you write:

```kotlin
remember { ... }
```

or

```kotlin
viewModel.someState
```

pause for a moment.

Ask yourself:

> **Who actually owns this information?**

If you can't answer that confidently,

you probably haven't chosen the right place to store it yet.

---

## What This Chapter Does NOT Answer

You might still be wondering:

- Should email always be local state?
- Should loading always live in the ViewModel?
- Should every screen be stateless?

Those are excellent questions.

But they're different questions.

This chapter teaches **how to think about ownership**.

The next chapters teach how to apply that thinking.

---

## Revision

### Core Idea

```text
New State
      │
      ▼
Who Owns It?
      │
      ▼
Choose The Owner
      │
      ▼
Choose The State Container
```

### Engineering Mindset

Don't ask:

> **"Where can I store this state?"**

Ask:

> **"Who is responsible for this information?"**

### Key Insight

State ownership is an architectural decision.

The choice of `remember`, `ViewModel`, or `StateFlow` is simply an implementation detail that follows from that decision.

---

## One Remaining Question

We've learned how to identify the correct owner for a piece of state.

A natural question appears.

> **Once we've identified the owner, should the composable own the state itself or receive it from somewhere else?**

That leads us to the next concept.

**Stateless vs Stateful Composables**
