# viewModelScope

## Looking Back

In the previous chapters we learned:

```text
ViewModel

↓

Owns Screen State
```

and

```text
ViewModel

↓

Has A Lifetime
```

A natural question appears:

```text
If A ViewModel Has A Lifetime, Can Coroutines Automatically Follow That Lifetime?
```

---

## A Simple Observation

Suppose the user performs:

```text
Open Search

↓

Type

"Chicken Biryani"

↓

Network Request Starts
```

Now the user presses:

```text
Back

↓

Leave Search Screen
```

A natural question appears:

```text
Should The Network Coroutine Continue Running?
```

The answer is:

```text
No.
```

When the screen is gone, work that belongs to that screen should also stop.

---

## viewModelScope

A `viewModelScope` represents:

```text
A CoroutineScope Owned By A ViewModel
```

Conceptually:

```text
ViewModel

↓

viewModelScope

↓

Coroutines
```

The ViewModel owns screen state.

It also owns the CoroutineScope used for screen-related asynchronous work.

---

## Android Example

Suppose the user performs:

```text
Open Search

↓

Type

"Chicken Biryani"

↓

Start Network Request
```

Conceptually:

```text
SearchViewModel

↓

viewModelScope

↓

Search Coroutine

↓

Repository

↓

Update Screen State
```

Now the user presses:

```text
Back
```

Conceptually:

```text
Logical Screen Removed

↓

ViewModel Cleared

↓

viewModelScope Cancelled

↓

Search Coroutine Cancelled
```

The coroutine naturally follows the lifetime of its owner.

---

## Minimal Code

```kotlin
viewModelScope.launch {

    repository.search(query)

}
```

This launches a coroutine inside the ViewModel's CoroutineScope.

---

## Production Code

```kotlin
class SearchViewModel : ViewModel() {

    fun search(query: String) {

        viewModelScope.launch {

            val results =
                repository.search(query)

            _uiState.value =
                SearchUiState(results)

        }

    }

}
```

### How To Read It

Don't read:

```text
viewModelScope

↓

launch()
```

Read:

```text
SearchViewModel

↓

Owns viewModelScope

↓

Launch Search Coroutine

↓

Update Screen State
```

The coroutine belongs to the ViewModel,

so its lifetime naturally follows the ViewModel's lifetime.

---

## Production Notes

```text
• viewModelScope is a CoroutineScope.

• It is owned by a ViewModel.

• Coroutines launched inside it are automatically cancelled when the ViewModel is cleared.

• Screen-related asynchronous work typically belongs in viewModelScope.
```

---

## Another Common Misconception

A common misconception is:

```text
viewModelScope = A Special Android Coroutine
```

Incorrect.

```text
CoroutineScope

↓

General Kotlin Concept

-------------------------

viewModelScope

↓

CoroutineScope Owned By A ViewModel
```

Android is applying an existing Kotlin concept,

not introducing a new concurrency model.

---

## Putting Everything Together

```text
ViewModel

↓

Owns Screen State

↓

Owns viewModelScope

↓

Owns Coroutines

↓

Updates StateFlow

↓

Activity / Fragment Displays State
```

Everything follows the same ownership chain.

---

## Reading Production Code

```kotlin
class HomeViewModel : ViewModel() {

    fun refresh() {

        viewModelScope.launch {

            val users =
                repository.loadUsers()

            _uiState.value =
                HomeUiState(users)

        }

    }

}
```

Read it as:

```text
HomeViewModel

↓

Owns CoroutineScope

↓

Launch Async Work

↓

Repository

↓

Update Screen State
```

Instead of seeing unrelated APIs,

recognize a single responsibility:

```text
Perform Screen Work Within The ViewModel Lifetime
```

---

## A Natural Question

A ViewModel now preserves screen state

across Activity recreation.

But another question appears:

```text
Rotate Device

↓

ViewModel Survives

↓

State Survives

✅

-------------------------

Kill App Process

↓

Open App Again

↓

Does State Still Survive?
```

That question leads us to the next chapter:

```text
SavedStateHandle
```

---

## Revision

### Core Idea

```text
viewModelScope = CoroutineScope Owned By A ViewModel
```

### Mental Model

```text
ViewModel

↓

viewModelScope

↓

Coroutines
```

### Production Recognition

```kotlin
viewModelScope.launch {

    ...
}
```

↓

```text
Launch Screen-related Work That Automatically Ends With The ViewModel
```

### Previous Concept

```text
ViewModel Lifecycle
```

### Next Concept

```text
SavedStateHandle
```
