## Looking Back

In the previous chapters we learned:

```text
Configuration Change

↓

ViewModel Survives

↓

Screen State Survives
```

Now consider a different situation.

The user performs:

```text
Open Search

↓

Type

"Chicken Biryani"

↓

Press Home

↓

Open YouTube

↓

Open Maps

↓

Phone Starts Running Low On Memory

↓

Android Frees Memory By Killing Background App Processes

↓

User Opens The Search App Again
```

From the user's perspective,

the application was never intentionally closed.

They simply switched to other applications and later returned.

A natural question appears:

```text
Should The User Continue From Where They Left Off?
```

Most users would expect: 
```text
Yes.
```

---

## Why ViewModel Isn't Enough

We already know:

```text
Android Process

↓

Application

↓

Activity

↓

ViewModelStore

↓

ViewModel
```

If the Android Process is killed:

```text
Android Process

↓

Destroyed

↓

Everything Inside It

↓

Destroyed
```

including:

```text
Application

Activity

ViewModelStore

ViewModel
```

A ViewModel solves:

```text
Configuration Changes
```

It does **not** solve:

```text
Process Death
```

---

## What Should Actually Be Saved?

Imagine a Search screen containing:

```text
Search Query

Search Results

Recommendations

Selected Filter

Sort Order

Scroll Position
```

Should Android save all of this?

Probably not.

Instead, ask:

```text
What Is The Smallest Amount Of Information Needed To Rebuild This Screen?
```

For this example:

```text
Search Query

Selected Filter

Sort Order

Scroll Position
```

Everything else can be regenerated.

---

## SavedStateHandle

A SavedStateHandle represents:

```text
The Minimum Information Needed To Reconstruct Screen State
```

It does not replace the ViewModel.

It does not replace the Repository.

It simply remembers enough information to rebuild the screen after process death.

---

## Mental Model

```text
Repository

↓

Owns Data

-------------------------

ViewModel

↓

Owns Live Screen State

-------------------------

SavedStateHandle

↓

Owns The Recipe To Rebuild Screen State
```

Each object has a different responsibility.

---

## Android Example

Suppose the user searches:

```text
"Chicken Biryani"
```

SavedStateHandle stores:

```text
query = "Chicken Biryani"
```

Later:

```text
Process Dies

↓

New Process

↓

New ViewModel

↓

Read query

↓

Repository.search(query)

↓

Fresh Screen State
```

The screen is reconstructed,

not restored from a large cache.

---

## Minimal Code

```kotlin
class SearchViewModel(
    private val savedStateHandle: SavedStateHandle
) : ViewModel()
```

This ViewModel can save and retrieve reconstruction information.

---

## Production Code

```kotlin
class SearchViewModel(
    private val savedStateHandle: SavedStateHandle,
    private val repository: SearchRepository
) : ViewModel() {

    var query: String
        get() = savedStateHandle["query"] ?: ""
        set(value) {
            savedStateHandle["query"] = value
        }

    fun search() {

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
SavedStateHandle

↓

get()

↓

set()
```

Read:

```text
Remember Important Inputs

↓

Repository Rebuilds Data

↓

ViewModel Rebuilds Screen State
```

---

## Production Notes

```text
• ViewModel survives configuration changes.

• ViewModel does not survive process death.

• SavedStateHandle stores only small pieces of information needed to reconstruct screen state.

• Large objects, network responses and databases belong in the Repository, not in SavedStateHandle.
```

---

## Another Common Misconception

A common misconception is:

```text
SavedStateHandle = Persistent Storage
```

Incorrect.

```text
Repository

↓

Persistent Data

-------------------------

SavedStateHandle

↓

Small Reconstruction State
```

SavedStateHandle remembers:

```text
Product ID

Search Query

Selected Tab
Filter
Scroll Position
```

not:

```text
Entire Product

User List

Search Results

Network Response
```

---

## Platform Perspective

The API is Android-specific.

The architectural idea is not.

Many applications across platforms persist only the minimum information required to reconstruct screen state.

For example:

```text
Product ID

↓

Repository

↓

Reload Product
```

instead of:

```text
Entire Product Object

↓

Persist

↓

Restore
```

Keeping reconstruction information small helps ensure that users receive fresh data instead of stale cached state.

---

## Putting Everything Together

```text
Repository

↓

Owns Data

-------------------------

SavedStateHandle

↓

Owns Reconstruction Information

-------------------------

ViewModel

↓

Owns Live Screen State

-------------------------

Activity / Fragment

↓

Displays Screen
```

Each layer has one responsibility.

---

## Reading Production Code

```kotlin
class ProductViewModel(
    savedStateHandle: SavedStateHandle,
    repository: ProductRepository
) : ViewModel() {

    val productId =
        savedStateHandle["productId"]!!

    val product =
        repository.load(productId)

}
```

Read it as:

```text
SavedStateHandle

↓

Remember Product ID

↓

Repository

↓

Load Fresh Product

↓

ViewModel

↓

Build Screen State
```

instead of:

```text
SavedStateHandle API

↓

Repository API
```

Focus on responsibilities rather than individual methods.

---

## A Natural Question

We have now learned:

```text
ViewModel

↓

ViewModelStore

↓

ViewModel Lifecycle

↓

viewModelScope

↓

SavedStateHandle
```

Another question appears:

```text
Can Multiple Screens Share The Same ViewModel?

If So, Who Owns It? And

When Is It Destroyed?
```

That question leads us to the next chapter:

```text
ViewModel Owners
```

---

## Revision

### Core Idea

```text
SavedStateHandle = The Minimum Information Needed To Reconstruct Screen State After Process Death
```

### Mental Model

```text
Repository

↓

Owns Data

-------------------------

SavedStateHandle

↓

Owns Reconstruction Information

-------------------------

ViewModel

↓

Owns Live Screen State
```

### Production Recognition

```kotlin
savedStateHandle["productId"]
```

↓

```text
Remember Product ID

↓

Repository Reloads Everything Else
```

### Previous Concept

```text
viewModelScope
```

### Next Concept

```text
ViewModel Owners
```
