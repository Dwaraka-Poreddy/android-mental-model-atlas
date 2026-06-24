# Repository

## Looking Back

In the previous chapters we learned:

```text
ViewModel

↓

Owns Screen State
```

A natural question appears:

```text
Who Should Own Application Data?
```

---

## A Simple Observation

Imagine an e-commerce application.

```text
Home Screen

↓

Profile Screen

↓

Cart Screen
```

Every screen needs:

```text
Current User
```

One possible design is:

```text
HomeViewModel

↓

Current User
```

```text
ProfileViewModel

↓

Current User
```

```text
CartViewModel

↓

Current User
```

Now the same data exists in multiple places.

---

## The Problem

Suppose the user changes their name.

```text
"Dwaraka"

↓

"Dwaraka Poreddy"
```

Now:

```text
HomeViewModel ?

ProfileViewModel ?

CartViewModel ?
```

Which copy should be updated?

Keeping multiple copies of the same application data quickly becomes difficult and error-prone.

---

## Repository

A Repository represents:

```text
The Owner Of Application Data
```

Instead of:

```text
HomeViewModel

↓

Current User
```

```text
ProfileViewModel

↓

Current User
```

```text
CartViewModel

↓

Current User
```

we have:

```text
UserRepository

↓

Current User

↓

HomeViewModel

↓

ProfileViewModel

↓

CartViewModel
```

Now:

```text
One Owner

↓

Many Readers
```

Every screen observes the same application data.

---

## Another Responsibility

A ViewModel asks:

```text
Give Me The Current User
```

The Repository decides:

```text
Memory or

Database or

Network or

A Combination Of Them
```

The ViewModel does not need to know where the data comes from.

Its responsibility is only to build screen state.

---

## Mental Model

```text
ViewModel

↓

Owns Screen State

-------------------------

Repository

↓

Owns Application Data

-------------------------

Network / Database

↓

Provide Data Sources
```

Each layer has exactly one responsibility.

---

## Minimal Code

```kotlin
class UserRepository {

    suspend fun getUser(): User

}
```

The ViewModel requests data from the Repository instead of directly accessing a network or database.

---

## Production Code

```kotlin
class HomeViewModel(
    private val repository: UserRepository
) : ViewModel() {

    fun refresh() {

        viewModelScope.launch {

            val user =
                repository.getUser()

            _uiState.value =
                HomeUiState(user)

        }

    }

}
```

### How To Read It

Don't read:

```text
HomeViewModel

↓

Repository

↓

getUser()
```

Read:

```text
HomeViewModel

↓

Requests Application Data

↓

Repository

↓

Owns That Data

↓

Returns It

↓

ViewModel Builds Screen State
```

Focus on responsibilities rather than method calls.

---

## Production Notes

```text
• ViewModels own screen state.

• Repositories own application data.

• Multiple ViewModels can safely share the same Repository.

• A Repository decides where data comes from.

• ViewModels should not need to know whether data comes from memory, a database or a network.
```

---

## Deep Dive

### Ownership

Compare the responsibilities:

```text
ViewModel

↓

Owns Screen State

-------------------------

Repository

↓

Owns Application Data
```

The ViewModel answers:

```text
What Should This Screen Display?
```

The Repository answers:

```text
What Is The Current Application Data?
```

---

### Storage

A Repository is **not** a storage technology.

It is an owner.

Internally it may obtain data from:

```text
Memory

↓

Database

↓

Network

↓

Any Combination Of Them
```

The storage mechanism is an implementation detail.

The architectural responsibility remains the same:

```text
Repository

↓

Owns Application Data
```

---

### Scope

Repositories are typically shared across multiple ViewModels.

```text
UserRepository

↓

HomeViewModel

↓

ProfileViewModel

↓

CartViewModel
```

This avoids maintaining multiple copies of the same data.

---

## Architectural Perspective

Throughout this Atlas we consistently separate responsibilities:

```text
ViewModel

↓

Owns Screen State

-------------------------

Repository

↓

Owns Application Data
```

This is an architectural decision rather than an implementation detail.

A production application may internally use:

```text
Network

↓

Database

↓

Memory Cache

↓

Repository
```

or even multiple repositories.

Those implementation choices do not change the core responsibility:

```text
Repository

↓

Owns Application Data
```

---

## Another Common Misconception

A common misconception is:

```text
Repository = API Wrapper
```

Incorrect.

A Repository may obtain data from:

```text
Memory

Database

Network
```

or any combination of them.

Its responsibility is:

```text
Own Application Data
```

not:

```text
Call A Specific API
```

---

## Putting Everything Together

```text
Repository

↓

Application Data

↓

ViewModel

↓

Screen State

↓

Activity / Fragment

↓

UI
```

Application data and screen state have different owners and different responsibilities.

---

## Reading Production Code

```kotlin
class ProfileViewModel(
    private val repository: UserRepository
)
```

Read it as:

```text
ProfileViewModel

↓

Needs User Data

↓

UserRepository

↓

Owns User Data
```

instead of:

```text
ViewModel

↓

Calls API
```

Always identify the owner of the data.

---

## A Natural Question

Suppose:

```text
HomeViewModel

↓

UserRepository

↓

Current User
```

and

```text
ProfileViewModel

↓

UserRepository

↓

Current User
```

If the user changes,

how do both screens automatically observe the same updated value?

That question leads us to the next chapter:

```text
Single Source Of Truth
```

---

## Revision

### Core Idea

```text
Repository = Owner Of Application Data
```

### Mental Model

```text
Repository

↓

Application Data

↓

ViewModel

↓

Screen State
```

### Production Recognition

```kotlin
private val repository: UserRepository
```

↓

```text
This ViewModel Requests Application Data From Its Owner
```

### Previous Concept

```text
ViewModel Owners
```

### Next Concept

```text
Single Source Of Truth
```
