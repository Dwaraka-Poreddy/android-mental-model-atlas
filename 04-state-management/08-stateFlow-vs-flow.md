# StateFlow vs Flow

## Looking Back

In previous chapters we learned:

```text
Flow

↓

Represents Values Moving Over Time
```

and

```text
StateFlow

↓

Represents The Current State + Future State Updates
```

Both are reactive streams.

Both can be collected.

Both deliver values over time.

A natural question appears:

```text
When Should We Use Flow And

When Should We Use StateFlow?
```

---

## Two Different Problems

Although they look similar, they are designed to solve different problems.

Flow focuses on:

```text
Values Moving Through A Pipeline
```

StateFlow focuses on:

```text
The Current Situation Of Something
```

The difference is not the API.

The difference is:

```text
What They Represent
```

---

## Example 1

### Search Suggestions

Imagine a search box.

The user types:

```text
H

↓

He

↓

Hel

↓

Hell

↓

Hello
```

Each value starts a new search request.

We are interested in:

```text
New Queries As They Arrive
```

The previous queries naturally become less important.

Conceptually:

```text
Search Queries

↓

Flow
```

---

## Example 2

### Weather Screen

A weather screen currently shows:

```text
Location: Hyderabad

Temperature: 31°C

Condition: Sunny
```

At any moment the application already has:

```text
One Current Weather State
```

Whenever the temperature changes:

```text
31°C

↓

32°C

↓

33°C
```

the current state changes.

Conceptually:

```text
Current Weather

↓

StateFlow
```

---

## Example 3

### Music Player

A music player displays:

```text
Song: Believer

Playing: true

Progress: 01:42
```

These values together describe:

```text
The Current Playback State
```

As playback continues:

```text
01:42

↓

01:43

↓

01:44
```

the current state changes.

This naturally maps to:

```text
StateFlow
```

---

## Example 4

### Download Progress

Imagine downloading a large file.

The application receives:

```text
10%

↓

20%

↓

30%

↓

40%

↓

50%
```

Each update represents new information arriving over time.

We are interested in:

```text
The Incoming Updates
```

rather than a persistent application state.

Conceptually:

```text
Download Progress Updates

↓

Flow
```

---

## Another Mental Model

Imagine a river.

Water continuously flows past you.

You observe:

```text
Whatever Flows Next
```

This resembles:

```text
Flow
```

---

Now imagine a lake.

At every moment there is:

```text
One Current Lake
```

Its water level may rise or fall over time, but it always represents:

```text
The Current Situation
```

This resembles:

```text
StateFlow
```

Conceptually:

```text
River

↓

Flow

-------------------------

Lake

↓

StateFlow
```

---

## Android Example

Suppose a repository performs searches.

```kotlin
fun searchUsers(
    query: String
): Flow<List<User>>
```

Each query produces a new sequence of results.

---

Now consider a ViewModel exposing UI state.

```kotlin
val uiState: StateFlow<SearchUiState>
```

The UI always needs to know:

```text
The Current Screen State
```

while continuing to receive updates whenever it changes.

---

## StateFlow Is A Specialized Flow

StateFlow is still:

```text
A Flow
```

It follows the same reactive programming model and is collected in exactly the same way.

```kotlin
viewModel.uiState.collect { state ->

    println(state)

}
```

What makes it different is its purpose.

Instead of representing:

```text
Values Moving Through A Pipeline
```

it represents:

```text
The Current State Of Something
```

---

## Another Common Misconception

A common misconception is:

```text
Flow And StateFlow Are Competitors
```

They are not.

Each is designed for a different kind of information.

```text
Search Queries

↓

Flow

-------------------------

Current Login State

↓

StateFlow
```

---

## Another Common Misconception

A common misconception is:

```text
Every Flow Should Become A StateFlow
```

Not every stream represents state.

Many streams naturally model:

```text
Incoming Values

Events

Requests

Updates
```

and are better represented as regular Flow.

---

## A Simple Decision Rule

Whenever you are unsure, ask:

```text
Does This Represent The Current Situation?
```

If the answer is:

```text
Yes
```

it is usually a:

```text
StateFlow
```

If instead you are primarily interested in:

```text
Values As They Arrive
```

it is usually a:

```text
Flow
```

---

## Putting Everything Together

```text
Current Shopping Cart

↓

StateFlow

-------------------------

Search Queries

↓

Flow

-------------------------

Current Music Player State

↓

StateFlow

-------------------------

Download Updates

↓

Flow
```

---

## The Key Mental Model

Think of the difference like this:

```text
River

↓

Observe

Whatever Flows Next

↓

Flow

-------------------------

Lake

↓

Observe The Current Lake As It Changes

↓

StateFlow
```

The distinction is not about syntax.

It is about:

```text
What The Stream Represents
```

---

## Revision Table

| Section | Content |
|----------|----------|
| Core Idea | Flow and StateFlow solve different problems |
| Flow Represents | Values arriving over time |
| StateFlow Represents | Current state + future state updates |
| Mental Model | River vs Lake |
| Android Examples | Search, Weather, Music Player, UI State |
| Decision Rule | Current situation → StateFlow, Incoming values → Flow |
| Previous Concept | MutableStateFlow |
| Next Concept | SharedFlow |
