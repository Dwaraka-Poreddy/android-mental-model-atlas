# stateIn

## Looking Back

In previous chapters we learned that:

```text
Flow

↓

Represents Values Moving Over Time
```

and

```text
StateFlow

↓

Represents
The Current State + Future State Updates
```

Both are useful, but they solve different problems.

A natural question appears:

```text
What If We Already Have A Flow, But We Need A StateFlow?
```

---

## A Simple Observation

Suppose a repository exposes weather updates.

```kotlin
fun observeWeather(): Flow<Weather>
```

The repository is already producing a stream of weather information.

Now a ViewModel wants to expose:

```text
The Current Weather

+

Future Weather Updates
```

to the UI.

Should we rewrite the repository to produce a StateFlow instead?

Usually:

```text
No
```

It is often more useful to transform the existing Flow into a StateFlow.

---

## stateIn

`stateIn()` converts:

```text
Flow

↓

StateFlow
```

Conceptually:

```text
Flow

↓

stateIn()

↓

StateFlow
```

It allows an existing Flow to be represented as state without changing the original Flow itself.

---

## Another Mental Model

Imagine a news agency continuously publishing updates.

```text
News Update

↓

News Update

↓

News Update
```

This is simply a stream of information.

Now imagine installing a digital display board.

The board always shows:

```text
The Latest News
```

Whenever new information arrives, the board updates automatically.

Conceptually:

```text
News Stream

↓

Display Board

↓

Always Shows The Latest News
```

`stateIn()` plays the role of that display board.

It converts a stream of updates into something that always represents the latest known state.

---

## Android Example

Suppose a repository exposes weather updates.

```kotlin
class WeatherRepository {

    fun observeWeather(): Flow<Weather>

}
```

A ViewModel can expose state like this:

```kotlin
val weatherState =
    repository
        .observeWeather()
        .stateIn(
            scope = viewModelScope,
            started = SharingStarted.WhileSubscribed(),
            initialValue = Weather.Loading
        )
```

The important idea is not the parameters.

The important transformation is:

```text
Flow

↓

stateIn()

↓

StateFlow
```

---

## Another Android Example

Suppose a repository exposes search results.

```kotlin
fun searchUsers(
    query: String
): Flow<List<User>>
```

A ViewModel may want to expose:

```text
Current Search State
```

instead of a plain Flow.

Again:

```text
Flow

↓

stateIn()

↓

StateFlow
```

No changes are required to the repository itself.

---

## A Small Observation

The `stateIn()` function requires additional information such as:

```text
Scope

Sharing Strategy

Initial Value
```

These determine how the resulting StateFlow behaves.

We will understand each of them in the following chapters.

For now, it is enough to remember:

```text
stateIn()

↓

Creates A StateFlow From An Existing Flow
```

---

## Another Common Misconception

A common misconception is:

```text
stateIn() Creates A New Kind Of Flow
```

It does not.

Its purpose is simply:

```text
Flow

↓

StateFlow
```

---

## Another Common Misconception

A common misconception is:

```text
Repositories Must Always Expose StateFlow
```

Not necessarily.

Repositories often expose Flow naturally.

The ViewModel can convert that Flow into a StateFlow when the UI needs:

```text
The Current State

+

Future Updates
```

---

## Putting Everything Together

```text
Repository

↓

Flow

↓

stateIn()

↓

StateFlow

↓

UI
```

The original Flow remains unchanged.

`stateIn()` simply creates a StateFlow representation of it.

---

## The Key Mental Model

Think of a continuously updating news feed.

```text
News Stream

↓

stateIn()

↓

Display Board

↓

Always Shows The Latest News
```

Similarly:

```text
Flow

↓

stateIn()

↓

StateFlow
```

The stream continues to produce updates,

while the resulting StateFlow always represents the latest known state.

---

> **You'll see this in...**
> - **DOC 5 — Android Lifecycle & Architecture**, where ViewModels call `stateIn()` to convert Repository Flows into StateFlows that the UI can safely observe
> - **DOC 13 — Data Architecture**, where `stateIn(WhileSubscribed(5000))` keeps the upstream alive for 5 seconds after the last subscriber — surviving screen rotation without restarting expensive database queries

---

## Revision Table

| Section | Content |
|----------|----------|
| Core Idea | `stateIn()` converts an existing Flow into a StateFlow |
| Mental Model | News stream → Digital display board |
| Input | Flow |
| Output | StateFlow |
| Android Example | Repository Flow → ViewModel StateFlow |
| Common Misconception | `stateIn()` does not create a new kind of Flow |
| Previous Concept | Channel |
| Next Concept | initialValue |
