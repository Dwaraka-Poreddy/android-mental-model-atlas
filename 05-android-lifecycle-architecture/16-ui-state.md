# UI State

## Observation

A Repository owns application data.

```text
UserRepository

↓

Current User
```

A screen, however, needs much more than application data.

Consider a Login screen.

The UI needs to know:

```text
Email

Password

Loading State

Error Message

Login Button Enabled
```

A natural question appears:

```text
Who Owns The Complete Current State Of This Screen?
```

---

## A Simple Example

Suppose the Repository provides:

```kotlin
data class User(
    val firstName: String,
    val lastName: String,
    val premium: Boolean
)
```

The current screen also has:

```text
User Input

↓

Email

Password

-------------------------

Network Status

↓

Loading

-------------------------

Validation

↓

Login Enabled
```

The UI should not combine all of these pieces itself.

Instead, the ViewModel produces one object representing the current screen.

---

## UI State

UI State represents:

```text
The Complete Current State Of A Screen Ready To Render
```

It combines:

```text
Repository Data

+

User Input

+

UI Flags

+

Loading State

+

Error State
```

into a single object that the UI can render directly.

---

## Mental Model

```text
Repository

↓

Application Data

↓

ViewModel

↓

Transforms

↓

UI State

↓

Compose Renders It
```

The Repository owns application data.

The ViewModel owns UI State.

The UI simply renders it.

---

## Minimal Code

```kotlin
data class LoginUiState(
    val email: String,
    val password: String,
    val isLoading: Boolean,
    val errorMessage: String?,
    val isLoginEnabled: Boolean
)
```

This object represents everything the current screen needs.

---

## Production Code

```kotlin
class LoginViewModel(
    private val repository: UserRepository
) : ViewModel() {

    val uiState: StateFlow<LoginUiState> = ...

}
```

The ViewModel exposes a single UI State for the screen.

The UI observes it and renders it.

---

## How To Read It

Don't read:

```text
LoginUiState

↓

email

↓

password

↓

isLoading
```

Read:

```text
ViewModel

↓

Owns The Complete Current Screen State

↓

UI Renders It
```

Focus on responsibility rather than individual fields.

---

## Production Notes

```text
• A ViewModel owns UI State.

• UI State represents the complete current state of a screen.

• UI State is commonly derived from Repository data, user interactions and transient UI conditions.

• The UI should primarily render UI State instead of deriving its own state.
```

---

## Note

Many Android resources use the terms:

```text
UI State

Screen State
```

interchangeably.

Throughout this Atlas, they refer to the same concept:

```text
The Complete Current State Of A Screen Ready To Render
```

For consistency, this Atlas uses the term **UI State**.

---

## Revision

### Core Idea

```text
UI State = The Complete Current State Of A Screen Ready To Render
```

### Mental Model

```text
Repository

↓

Application Data

↓

ViewModel

↓

UI State

↓

UI
```

### Production Recognition

```kotlin
StateFlow<LoginUiState>
```

↓

```text
ViewModel

↓

Owns The Current UI State Of This Screen
```

### Previous Concept

```text
Single Source Of Truth
```

### Next Concept

```text
Business State
```
