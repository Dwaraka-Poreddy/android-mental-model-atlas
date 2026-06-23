# ViewModelStore

## Looking Back

In the previous chapter we learned:

```text
Activity

↓

ViewModel

↓

Screen State
```

and

```text
Configuration Change

↓

Old Activity Destroyed

↓

New Activity Created

↓

Same ViewModel
```

A natural question appears:

```text
How Does Android Know Which ViewModel To Return?
```

---

## ViewModelStore

A ViewModelStore represents:

```text
A Place That Stores ViewModels
```

Conceptually:

```text
Activity

↓

ViewModelStore

↓

ViewModels
```

Instead of creating a new ViewModel every time,

Android first looks inside the ViewModelStore.

---

## A Simple Observation

Suppose a screen uses two ViewModels:

```kotlin
private val searchViewModel by viewModels<SearchViewModel>()

private val filtersViewModel by viewModels<FiltersViewModel>()
```

Conceptually, the ViewModelStore contains:

```text
ViewModelStore

├── SearchViewModel
└── FiltersViewModel
```

Each ViewModel has its own entry.

---

## How Android Finds The Correct ViewModel

When the Activity requests:

```kotlin
private val searchViewModel by viewModels<SearchViewModel>()
```

Android conceptually performs:

```text
Request SearchViewModel

↓

Look Inside ViewModelStore

↓

Already Exists?

    Yes → Return Existing ViewModel

    No  → Create New ViewModel
            Store It
            Return It
```

The Activity does not remember the ViewModel.

It simply asks the ViewModelStore for it.

---

## Minimal Code

```kotlin
private val viewModel by viewModels<SearchViewModel>()
```

This is conceptually equivalent to:

```text
Request SearchViewModel

↓

Return Existing Or Create New
```

---

## Production Code

```kotlin
class SearchActivity : ComponentActivity() {

    private val searchViewModel by viewModels<SearchViewModel>()

}
```

### How To Read It

Don't read:

```text
viewModels()
```

Read:

```text
Give Me The SearchViewModel For This Screen
```

If one already exists,

reuse it.

Otherwise,

create a new one.

---

## Production Notes

```text
• A ViewModelStore stores ViewModels for a screen.

• Multiple ViewModels can exist inside the same ViewModelStore.

• Activities request ViewModels from the ViewModelStore instead of creating them directly.

• Existing ViewModels are reused whenever possible.
```

---

## Another Common Misconception

A common misconception is:

```text
Activity Keeps A Direct Reference To Every ViewModel
```

Conceptually:

```text
Activity

↓

Requests ViewModel

↓

ViewModelStore

↓

Returns Matching ViewModel
```

The ViewModelStore is responsible for managing ViewModel instances.

---

## Putting Everything Together

```text
Activity

↓

Requests

↓

ViewModelStore

↓

Returns

↓

ViewModel

↓

Owns Screen State
```

Every object has a single responsibility.

---

## A Natural Question

We now understand:

```text
Activity

↓

ViewModelStore

↓

ViewModel
```

But another question appears:

```text
If The Activity Is Destroyed, Why Doesn't The ViewModelStore Disappear Too?
```

That question leads us to the next chapter:

```text
ViewModel Lifecycle
```

---

## Revision

### Core Idea

```text
ViewModelStore = Stores ViewModels
```

### Mental Model

```text
Activity

↓

Requests

↓

ViewModelStore

↓

Returns

↓

ViewModel
```

### Production Recognition

```kotlin
private val viewModel by viewModels<SearchViewModel>()
```

↓

```text
Request Existing ViewModel

Or

Create A New One
```

### Previous Concept

```text
ViewModel
```

### Next Concept

```text
ViewModel Lifecycle
```
