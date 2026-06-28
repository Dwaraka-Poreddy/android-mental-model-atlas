# Single Source Of Truth

## Observation

Imagine an e-commerce application.

```text
Home Screen

↓

Current User
```

```text
Profile Screen

↓

Current User
```

```text
Cart Screen

↓

Current User
```

Every screen needs the same application data.

A natural question appears:

```text
Who Should Own The Current User?
```

---

## The Problem

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

Suppose the user updates their name:

```text
"Dwaraka"

↓

"Dwaraka Poreddy"
```

Which copy should change?

If one ViewModel updates while another does not:

```text
Home

↓

Dwaraka Poreddy

-------------------------

Profile

↓

Dwaraka
```

the application becomes inconsistent.

---

## Single Source Of Truth

The solution is simple:

```text
One Data

↓

One Owner

↓

One Truth
```

Instead of every ViewModel owning its own copy:

```text
HomeViewModel

↓

Current User

-------------------------

ProfileViewModel

↓

Current User

-------------------------

CartViewModel

↓

Current User
```

use:

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

Now every screen reads the same data.

---

## Mental Model

```text
Repository

↓

One Owner

↓

Application Data

↓

Many Readers
```

The Repository owns the data.

ViewModels consume it.

---

## Minimal Code

```kotlin
class UserRepository {

    val currentUser: StateFlow<User>

}
```

Multiple ViewModels can observe the same value.

---

## Production Code

```kotlin
class HomeViewModel(
    private val repository: UserRepository
) : ViewModel() {

    val currentUser = repository.currentUser

}
```

```kotlin
class ProfileViewModel(
    private val repository: UserRepository
) : ViewModel() {

    val currentUser = repository.currentUser

}
```

---

## How To Read It

Don't read:

```text
HomeViewModel

↓

repository.currentUser
```

Read:

```text
HomeViewModel

↓

Requests Current User

↓

UserRepository

↓

Single Owner Of Current User
```

Both ViewModels observe the same source instead of maintaining separate copies.

---

## Production Notes

```text
• Application data should have a single owner.

• Multiple ViewModels should observe shared data instead of duplicating it.

• A Repository is a common owner for application data.

• One owner eliminates synchronization problems between multiple copies.
```

---

## Revision

### Core Idea

```text
One Data

↓

One Owner

↓

One Truth
```

### Mental Model

```text
Repository

↓

Application Data

↓

Many ViewModels
```

### Production Recognition

```kotlin
private val repository: UserRepository
```

↓

```text
This ViewModel Reads Shared Application Data Instead Of Owning Its Own Copy
```

### Previous Concept

```text
Repository
```

### Next Concept

```text
UI State
```
