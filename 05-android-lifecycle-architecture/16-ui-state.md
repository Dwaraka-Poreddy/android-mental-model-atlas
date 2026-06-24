# UI State

## Observation

Suppose a Repository provides the following application data:

```kotlin
data class User(
    val firstName: String,
    val lastName: String,
    val birthYear: Int,
    val premium: Boolean
)
```

A natural question appears:

```text
Can The UI Render This Object Directly?
```

Sometimes it can.

Often, it does not.

---

## A Simple Example

The Repository provides:

```text
firstName = "Dwaraka"

lastName = "Poreddy"

birthYear = 1998

premium = true
```

The screen wants to display:

```text
Welcome Dwaraka

Age: 28

Premium Member
```

Notice the difference.

The UI is not displaying:

```text
birthYear
```

It is displaying:

```text
Age
```

The UI is not displaying:

```text
premium = true
```

It is displaying:

```text
Premium Member Badge
```

The data has been transformed into something that is easier to render.

---

## UI State

UI State represents:

```text
Exactly What The UI Needs To Render
```

It is designed for presentation,

not for storage or business logic.

---

## Mental Model

```text
Repository

↓

Application Data

↓

ViewModel

↓

UI State

↓

Compose Renders UI State
```

The Repository owns application data.

The ViewModel transforms that data into UI State.

The UI simply renders it.

---

## Minimal Code

```kotlin
data class ProfileUiState(
    val welcomeText: String,
    val ageText: String,
    val showPremiumBadge: Boolean
)
```

This object contains exactly what the UI needs.

Nothing more.

---

## Production Code

```kotlin
class ProfileViewModel(
    private val repository: UserRepository
) : ViewModel() {

    val uiState = ProfileUiState(
        welcomeText = "Welcome ${user.firstName}",
        ageText = "${calculateAge(user.birthYear)} years",
        showPremiumBadge = user.premium
    )

}
```

---

## How To Read It

Don't read:

```text
ProfileUiState

↓

welcomeText

↓

ageText
```

Read:

```text
Repository

↓

Application Data

↓

ViewModel Transforms Data

↓

UI State

↓

UI Displays It
```

UI State is a presentation model,

not a repository model.

---

## Production Notes

```text
• UI State contains only information required for rendering.

• It is often derived from Repository data.

• Formatting, calculated values and visibility flags commonly belong in UI State.

• The UI should primarily render UI State instead of performing transformations itself.
```

---

## Revision

### Core Idea

```text
UI State

=

Exactly What The UI Needs To Render
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
data class ProfileUiState(...)
```

↓

```text
Presentation Model Built For Rendering
```

### Previous Concept

```text
Single Source Of Truth
```

### Next Concept

```text
Screen State
```
