# SharingStarted

## Looking Back

In the previous chapters we learned:

```text
Flow

↓

stateIn()

↓

StateFlow
```

We also learned that a StateFlow created using `stateIn()` needs an `initialValue`.

A natural question now appears:

```text
When Should The Resulting StateFlow Start Collecting The Original Flow?
```

Different applications may require different behavior.

`SharingStarted` exists to answer that question.

---

## SharingStarted

`SharingStarted` defines:

```text
When A StateFlow Created By stateIn() Should Start And Stop Collecting Its Original Flow
```

Conceptually:

```text
Flow

↓

stateIn()

↓

SharingStarted

↓

StateFlow
```

Different strategies simply make different choices about **when collection should begin and end**.

---

## Another Mental Model

Imagine a restaurant kitchen.

The kitchen is capable of preparing food.

A natural question appears:

```text
When Should The Kitchen Start Cooking?
```

There is no single correct answer.

Different restaurants may choose different strategies.

---

## Strategy 1 — Eagerly

One restaurant starts cooking as soon as it opens.

```text
Restaurant Opens

↓

Kitchen Starts

↓

Customers Arrive Later
```

The kitchen does not wait for anyone to place an order.

Similarly:

```text
SharingStarted.Eagerly

↓

Start Collecting Immediately
```

---

## Strategy 2 — Lazily

Another restaurant waits.

```text
Restaurant Opens

↓

Kitchen Waits

↓

First Customer Arrives

↓

Kitchen Starts
```

Nothing happens until someone actually needs the food.

Similarly:

```text
SharingStarted.Lazily

↓

Wait

↓

First Collector Appears

↓

Start Collecting
```

---

## Strategy 3 — WhileSubscribed

A third restaurant operates only while customers are present.

```text
Customers Present

↓

Kitchen Running

-------------------------

No Customers

↓

Kitchen Stops
```

Similarly:

```text
SharingStarted.WhileSubscribed()

↓

Start When Collectors Exist

↓

Stop When Collectors Leave
```

---

## Android Example

Suppose a ViewModel exposes weather state.

```kotlin
val weatherState =
    repository
        .observeWeather()
        .stateIn(
            scope = viewModelScope,
            started = SharingStarted.Eagerly,
            initialValue = Weather.Loading
        )
```

Changing the strategy is as simple as:

```kotlin
SharingStarted.Lazily
```

or

```kotlin
SharingStarted.WhileSubscribed()
```

The Flow remains the same.

Only the collection strategy changes.

---

## Comparing The Strategies

| Strategy | Starts Collecting | Stops Collecting |
|-----------|------------------|------------------|
| **Eagerly** | Immediately | When the owning scope ends |
| **Lazily** | When the first collector appears | When the owning scope ends |
| **WhileSubscribed** | When the first collector appears | When no collectors remain |

---

## Choosing A Strategy

Ask yourself:

```text
Should Collection Start Immediately?
```

If yes:

```text
Eagerly
```

Otherwise ask:

```text
Should Collection Stop When Nobody Is Observing?
```

If yes:

```text
WhileSubscribed
```

Otherwise:

```text
Lazily
```

---

# Advanced Configuration

The previous strategies answer:

```text
When Should Collection Start And Stop?
```

Sometimes applications need a little more control.

---

## Why Wait Before Stopping?

Imagine a user:

```text
Home Screen

↓

Settings

↓

Home Screen
```

all within one second.

If the application stops collecting immediately:

```text
Stop

↓

Start

↓

Stop

↓

Start
```

it may perform unnecessary work.

Sometimes it is better to wait briefly before stopping.

---

## stopTimeoutMillis

`WhileSubscribed()` can be configured to wait before stopping.

```kotlin
SharingStarted.WhileSubscribed(
    stopTimeoutMillis = 5000
)
```

Conceptually:

```text
Last Collector Leaves

↓

Wait 5 Seconds

↓

Still Nobody?

↓

Stop Collecting
```

If another collector appears during those five seconds:

```text
Collection Continues
```

without stopping and starting again.

---

## replayExpirationMillis

Earlier we learned:

```text
Replay

↓

Remember Previous Emissions
```

A natural question appears:

```text
Should They Be Remembered Forever?
```

Not necessarily.

`replayExpirationMillis` defines:

```text
How Long Replayed Values Should Be Kept
```

Conceptually:

```text
Replay Value

↓

Remember

↓

Time Passes

↓

Forget
```

instead of remembering it indefinitely.

---

## Another Common Misconception

A common misconception is:

```text
SharingStarted Changes The Flow
```

It does not.

The original Flow behaves exactly the same.

`SharingStarted` only decides:

```text
When The StateFlow Starts And Stops Collecting It
```

---

## Putting Everything Together

```text
Flow

↓

stateIn()

↓

initialValue

↓

SharingStarted

    ├── Eagerly
    │      Start Immediately
    │
    ├── Lazily
    │      Wait For First Collector
    │
    └── WhileSubscribed
           Start With Collectors
           Stop Without Collectors
```

Advanced configuration refines that behavior:

```text
WhileSubscribed

↓

stopTimeoutMillis

↓

Wait Before Stopping

-------------------------

Replay

↓

replayExpirationMillis

↓

Forget Old Replay Values
```

---

## The Key Mental Model

`SharingStarted` answers one simple question:

```text
When Should A StateFlow Created By stateIn() Start And Stop Collecting Its Original Flow?
```

The available strategies simply provide different answers:

```text
Eagerly

↓

Start Immediately

-------------------------

Lazily

↓

Wait For First Collector

-------------------------

WhileSubscribed

↓

Run Only While Collectors Exist
```

The advanced configuration options do not introduce new strategies.

They simply fine-tune those behaviors for real-world applications.

---

## Revision Table

| Section | Content |
|----------|----------|
| Core Idea | `SharingStarted` controls when a `stateIn()` StateFlow starts and stops collecting |
| Mental Model | Restaurant kitchen |
| Eagerly | Start immediately |
| Lazily | Start when first collector appears |
| WhileSubscribed | Start with collectors, stop when none remain |
| Advanced Configuration | `stopTimeoutMillis`, `replayExpirationMillis` |
| Previous Concept | `initialValue` |
| Next Concept | **Doc 5 — Android Lifecycle & Architecture** |
