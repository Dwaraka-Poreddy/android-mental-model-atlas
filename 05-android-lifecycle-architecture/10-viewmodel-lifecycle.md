# ViewModel Lifecycle

## Looking Back

In the previous chapter we learned:

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

A natural question appears:

```text
If The Activity Is Destroyed, Why Doesn't The ViewModelStore Disappear Too?
```

---

## A Simple Observation

Consider two different situations.

### Situation 1

```text
Open Search Screen

↓

Type

"Chicken Biryani"

↓

Rotate Device
```

The user is still on:

```text
The Same Screen
```

Only the Activity instance changes.

---

### Situation 2

```text
Open Search Screen

↓

Press Back
```

The user leaves the screen completely.

These two situations are fundamentally different.

---

## ViewModel Lifecycle

A ViewModel exists for:

```text
The Lifetime Of A Screen
```

Not for the lifetime of a particular Activity object.

Conceptually:

```text
Logical Screen

↓

ViewModelStore

↓

ViewModel

↓

Current Activity Instance
```

The Activity instance may change,

but the logical screen can continue to exist.

---

## Android Example

Suppose the user performs:

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
Logical Search Screen

↓

ViewModelStore

↓

SearchViewModel

↓

Old Activity Destroyed

↓

New Activity Created

↓

Same SearchViewModel
```

The screen continues to exist,

so the ViewModel continues to exist.

---

Now suppose the user performs:

```text
Open Search

↓

Press Back
```

Conceptually:

```text
Logical Search Screen

↓

Removed

↓

ViewModelStore Cleared

↓

SearchViewModel Cleared
```

The screen no longer exists,

so its ViewModel is no longer needed.

---

## Minimal Code

```kotlin
override fun onCleared() {
    super.onCleared()
}
```

This callback is invoked when the ViewModel is permanently removed.

---

## Production Code

```kotlin
class SearchViewModel : ViewModel() {

    override fun onCleared() {
        super.onCleared()

        // Cleanup work if required
    }

}
```

### How To Read It

Don't read:

```text
onCleared()
```

Read:

```text
Logical Screen

↓

Gone Forever

↓

ViewModel No Longer Needed
```

The callback represents the end of the ViewModel's lifetime.

---

## Production Notes

```text
• A ViewModel survives Activity recreation.

• A ViewModel does not survive when its screen is permanently removed.

• The lifetime of a ViewModel is tied to the lifetime of the logical screen.

• Cleanup work is typically performed in onCleared().
```

---

## Another Common Misconception

A common misconception is:

```text
ViewModel Lives Forever
```

Incorrect.

```text
Logical Screen Exists

↓

ViewModel Exists

-------------------------

Logical Screen Removed

↓

ViewModel Cleared
```

The lifetime of the ViewModel depends on the lifetime of its owner.

---

## Putting Everything Together

```text
Logical Screen

↓

ViewModelStore

↓

ViewModel

↓

StateFlow

↓

Screen State

↓

Current Activity Instance
```

The Activity displays the screen.

The ViewModel owns the screen state.

The ViewModelStore preserves the ViewModel while the logical screen continues to exist.

---

## A Natural Question

A ViewModel can now survive Activity recreation.

But another question appears:

```text
How Should A ViewModel Launch Coroutines That Automatically End When The ViewModel Is Cleared?
```

That question leads us to the next chapter:

```text
viewModelScope
```

---

## Revision

### Core Idea

```text
ViewModel Lives For The Lifetime Of A Logical Screen
```

### Mental Model

```text
Logical Screen

↓

ViewModelStore

↓

ViewModel

↓

Current Activity Instance
```

### Production Recognition

```kotlin
override fun onCleared() { }
```

↓

```text
ViewModel No Longer Needed

↓

Cleanup
```

### Previous Concept

```text
ViewModelStore
```

### Next Concept

```text
viewModelScope
```
