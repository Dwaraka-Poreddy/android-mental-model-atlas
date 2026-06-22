# SharedFlow

## Looking Back

In previous chapters we learned that:

```text
StateFlow

↓

Represents The Current State
```

It answers questions like:

```text
Current User

Current Cart

Current Playback State
```

Every observer is interested in the same:

```text
Current Situation
```

But not every piece of information in an application is state.

Sometimes something simply:

```text
Happens
```

---

## A New Observation

Suppose a payment succeeds.

Immediately:

- The UI should show a success banner.
- Analytics should record the payment.
- A logger should write a debug entry.

One thing happened:

```text
Payment Successful
```

Yet multiple parts of the application are interested in it.

Conceptually:

```text
                Payment Successful

                        │

        ┌───────-─────┼───────────┐
        │               │               │

        ▼               ▼               ▼

       UI          Analytics         Logger
```

---

## SharedFlow

SharedFlow represents a stream whose values can be:

```text
Shared Among Multiple Collectors
```

When a new value is emitted:

```text
Payment Successful
```

every interested collector can react to it independently.

Conceptually:

```text
One Emission

↓

Many Collectors
```

---

## Another Mental Model

Imagine an airport announcement.

A speaker announces:

```text
Flight 123 Is Ready For Boarding
```

Many people hear the same announcement:

```text
Passenger A

Passenger B

Airport Staff

Security
```

There is:

```text
One Announcement

↓

Many Listeners
```

A SharedFlow behaves in a similar way.

---

## Android Example

Suppose a ViewModel exposes payment events.

```kotlin
sealed interface PaymentEvent {

    data object PaymentSuccessful : PaymentEvent

}
```

The ViewModel exposes:

```kotlin
val paymentEvents: SharedFlow<PaymentEvent>
```

Different parts of the application can collect it.

```kotlin
// UI

viewModel.paymentEvents.collect {

    showSuccessBanner()

}
```

```kotlin
// Analytics

viewModel.paymentEvents.collect {

    analytics.logPaymentSuccess()

}
```

```kotlin
// Logger

viewModel.paymentEvents.collect {

    logger.debug("Payment successful")

}
```

Every collector reacts independently to the same emission.

---

## SharedFlow Is Not State

Suppose the emitted value is:

```text
Payment Successful
```

This does not become:

```text
Current Payment Successful
```

It simply represents:

```text
Something Happened
```

SharedFlow is commonly used for information that is naturally shared between multiple interested observers.

---

## Another Common Misconception

A common misconception is:

```text
SharedFlow Represents State
```

It does not.

State answers:

```text
What Is True Right Now?
```

SharedFlow commonly represents:

```text
Something Happened And Multiple Observers May Want To React
```

---

## Another Common Misconception

A common misconception is:

```text
SharedFlow Means Only One Collector
```

The word:

```text
Shared
```

highlights the opposite idea.

A single emission can be observed by multiple collectors at the same time.

---

## Putting Everything Together

```text
Payment Successful

        │

        ▼

    SharedFlow

        │

        ├────────► UI

        ├────────► Analytics

        └────────► Logger
```

One emission.

Multiple interested collectors.

---

## The Key Mental Model

Think of an airport announcement.

```text
One Announcement

↓

Many Listeners
```

Similarly:

```text
One Emission

↓

SharedFlow

↓

Many Collectors
```

The emphasis is not on storing state.

It is on:

```text
Sharing The Same Emission With Multiple Observers
```

---

## Revision Table

| Section | Content |
|----------|----------|
| Core Idea | A SharedFlow shares the same emissions among multiple collectors |
| Mental Model | Airport announcement |
| Represents | Shared emissions |
| Android Example | Payment event observed by UI, Analytics and Logger |
| Not Intended For | Representing current state |
| Previous Concept | StateFlow vs Flow |
| Next Concept | MutableSharedFlow |
