# ViewModel

## Looking Back

In the previous chapters we discovered:

```text
Activity

↓

Configuration Change

↓

Activity Recreated
```

A natural question appears:

```text
If Activities Can Be Recreated, Who Should Own The Screen State?
```

---

## ViewModel

A ViewModel represents:

```text
The Owner Of Screen State
```

Instead of storing state inside the Activity:

```text
Activity

↓

Search = "Chicken Biryani"
```

we move the ownership to:

```text
Activity

↓

ViewModel

↓

Search = "Chicken Biryani"
```

Now the Activity is responsible for displaying the screen,

while the ViewModel is responsible for owning its state.

Each object has a single responsibility.

---

## Android Example

Suppose the user:

```text
Open Search

↓

Type

"Chicken Biryani"

↓

Rotate Device
```

Conceptually:

```text
Old Activity

↓

ViewModel

↓

Search = "Chicken Biryani"

↓

Old Activity Destroyed

↓

New Activity Created

↓

Same ViewModel

↓

Search = "Chicken Biryani"
```

The screen is recreated,

but the screen state continues to live inside the ViewModel.

---

## Minimal Code

```kotlin
class SearchViewModel : ViewModel()
```

This declares a ViewModel responsible for one screen.

---

## Production Code

```kotlin
class SearchViewModel : ViewModel() {

    private val _query =
        MutableStateFlow("")

    val query =
        _query.asStateFlow()

}
```

### How To Read It

Read it as:

```text
SearchViewModel

↓

Owns Screen State

-------------------------

MutableStateFlow

↓

Mutable Screen State

(DOC 4)

-------------------------

asStateFlow()

↓

Expose Read-only State

(DOC 4)
```

Instead of seeing three unrelated APIs,

recognize a single responsibility:

```text
Own The Search State
```

---

## Production Notes

```text
• ViewModels own screen state.

• Activities and Fragments display that state.

• StateFlow is commonly used for representing screen state inside a ViewModel.

• Most production ViewModels expose immutable StateFlow while keeping MutableStateFlow private.
```

---

## Another Common Misconception

A common misconception is:

```text
ViewModel = The Screen
```

Incorrect.

```text
Activity / Fragment

↓

Displays Screen

-------------------------

ViewModel

↓

Owns Screen State
```

The UI and its state have different responsibilities.

---

## Putting Everything Together

```text
Activity / Fragment

↓

Displays

↓

ViewModel

↓

Owns

↓

StateFlow

↓

Current Screen State
```

Everything we've learned in DOC 4 naturally fits inside a ViewModel.

---

## A Natural Question

If the Activity is recreated, how does Android reconnect the new Activity with the existing ViewModel?

```text
Old Activity

↓

ViewModel

↓

Old Activity Destroyed

↓

New Activity Created

↓

Same ViewModel
```

Who preserves that ViewModel?

That question leads us to the next chapter:

```text
ViewModelStore
```

---

## Revision

### Core Idea

```text
ViewModel = Owner Of Screen State
```

### Mental Model

```text
Activity / Fragment

↓

Displays

-------------------------

ViewModel

↓

Owns State
```

### Production Recognition

```text
SearchViewModel

↓

MutableStateFlow

↓

Screen State
```

> **You'll see this in...**
> - **DOC 7 — Jetpack Compose**, where Composables observe ViewModel state via `collectAsState()` — ViewModel becomes the bridge between data and UI
> - **DOC 9 — Production Compose Patterns**, where state hoisting patterns determine whether state lives in the ViewModel or in a Composable
> - **DOC 11 — Remote Communication**, where ViewModels call Repository methods to trigger network requests
> - **DOC 14 — Dependency Injection**, where Hilt's `@HiltViewModel` automatically provides ViewModel dependencies
> - **DOC 16 — Testing Strategy**, where ViewModels are the primary unit under test for screen logic

### Previous Concept

```text
Configuration Changes
```

### Next Concept

```text
ViewModelStore
```
