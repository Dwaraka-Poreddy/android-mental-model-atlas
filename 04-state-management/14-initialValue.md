# initialValue

## Looking Back

In the previous chapter we learned that:

```text
Flow

↓

stateIn()

↓

StateFlow
```

`stateIn()` converts an existing Flow into a StateFlow.

A natural question appears.

StateFlow always represents:

```text
One Current Value
```

But what if the original Flow has not produced any value yet?

---

## A Simple Observation

Suppose a repository exposes weather updates.

```kotlin
fun observeWeather(): Flow<Weather>
```

Immediately after the application starts:

```text
No Weather Has Been Emitted Yet
```

However, a StateFlow must still answer:

```text
What Is The Current Weather?
```

The application cannot answer:

```text
There Is No Current Value
```

because a StateFlow always represents a current state.

Something must represent the state until the Flow produces its first value.

---

## initialValue

`initialValue` defines:

```text
The Current State Before The First Emission
```

Conceptually:

```text
Flow

↓

No Emission Yet

↓

initialValue

↓

First Emission

↓

Updated State

↓

Next Emission

↓

Updated State
```

---

## Another Mental Model

Imagine an airport departure board.

The system has just started.

No flight information has arrived yet.

Should the display remain completely empty?

Instead, it may show:

```text
Loading Flights...
```

A few moments later, the real information arrives.

```text
Flight AI-202 Boarding
```

Then later:

```text
Flight AI-202 Final Call
```

The display always has:

```text
One Current Display
```

Even before real flight information becomes available.

`initialValue` plays the same role.

---

## Android Example

Suppose a repository exposes weather updates.

```kotlin
class WeatherRepository {

    fun observeWeather(): Flow<Weather>

}
```

A ViewModel converts it into a StateFlow.

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

Initially the current state is:

```text
Loading
```

When the Flow emits:

```text
Sunny
```

the current state becomes:

```text
Sunny
```

Later it may become:

```text
Cloudy

↓

Rainy
```

---

## Another Example

Suppose a repository exposes a user profile.

```kotlin
fun observeProfile(): Flow<UserProfile>
```

The ViewModel converts it into a StateFlow.

```kotlin
.stateIn(
    scope = viewModelScope,
    started = SharingStarted.WhileSubscribed(),
    initialValue = UserProfile.Loading
)
```

Before any profile is loaded, the UI already has a meaningful state:

```text
Loading Profile...
```

instead of having no current state at all.

---

## Another Common Misconception

A common misconception is:

```text
initialValue Is The Default Value Forever
```

It is not.

It represents the current state only until the Flow produces its first emission.

Conceptually:

```text
Loading

↓

Sunny

↓

Cloudy

↓

Rainy
```

The initial value is simply the starting point.

---

## Another Common Misconception

A common misconception is:

```text
initialValue Changes The Original Flow
```

It does not.

The original Flow behaves exactly as before.

`initialValue` only defines:

```text
The Initial State Of The Resulting StateFlow
```

---

## Putting Everything Together

```text
Flow

↓

No Value Yet

↓

initialValue

↓

Current State Exists

↓

First Emission Arrives

↓

Current State Updates
```

---

## The Key Mental Model

`initialValue` answers one simple question:

```text
Before The Flow Produces Its First Value, What Should The Current State Be?
```

Once the first emission arrives, the StateFlow naturally continues updating with the values produced by the Flow.

---

## Revision Table

| Section | Content |
|----------|----------|
| Core Idea | `initialValue` defines the current state before the first Flow emission |
| Mental Model | Airport departure board |
| Purpose | Ensures StateFlow always has a current value |
| Android Example | Weather state, User profile state |
| Common Misconception | `initialValue` is only the starting state, not a permanent default |
| Previous Concept | `stateIn()` |
| Next Concept | `SharingStarted` |
