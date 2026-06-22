# MutableSharedFlow

## Looking Back

In the previous chapter we learned that:

```text
SharedFlow

↓

Shares Emissions With Multiple Collectors
```

Multiple parts of an application can observe the same stream of events.

A natural question appears:

```text
If Events Are Observed, Who Produces Them?
```

---

## A Simple Observation

Consider a payment flow.

When a payment succeeds, an event is produced:

```text
Payment Successful
```

Multiple parts of the application may react to it:

```text
UI

Analytics

Logger
```

But those components are only interested in:

```text
Observing The Event
```

Something still needs to:

```text
Produce The Event
```

Applications naturally separate these two responsibilities.

---

## SharedFlow And MutableSharedFlow

SharedFlow and MutableSharedFlow represent:

```text
The Same Event Stream
```

The difference is simply:

```text
SharedFlow

↓

Observe

-------------------------

MutableSharedFlow

↓

Observe

+

Emit
```

MutableSharedFlow is not a different kind of stream.

It represents the same stream while allowing its owner to produce new events.

---

## Another Mental Model

Imagine a company announcement system.

An announcement is sent:

```text
Office Closed Tomorrow
```

Every employee receives the announcement.

Most employees can only:

```text
Read The Announcement
```

Only authorized people can:

```text
Create A New Announcement
```

There is still:

```text
One Announcement System
```

The only difference is:

```text
Permission
```

Conceptually:

```text
Announcement System

↓

Reader

↓

Observe

-------------------------

Announcement System

↓

Administrator

↓

Observe

+

Announce
```

MutableSharedFlow works in the same way.

---

## Android Example

Suppose a ViewModel produces payment events.

```kotlin
sealed interface PaymentEvent {

    data object PaymentSuccessful : PaymentEvent

}
```

The ViewModel owns:

```kotlin
private val _paymentEvents =
    MutableSharedFlow<PaymentEvent>()
```

and exposes:

```kotlin
val paymentEvents: SharedFlow<PaymentEvent> =
    _paymentEvents
```

Consumers can observe the events but cannot produce them.

---

## Emitting Events

When a payment succeeds, the ViewModel emits an event.

```kotlin
_paymentEvents.emit(
    PaymentEvent.PaymentSuccessful
)
```

Every interested collector receives the same emission.

```kotlin
viewModel.paymentEvents.collect {

    // React to the event

}
```

---

## Another Android Example

A login flow may produce navigation events.

```kotlin
private val _navigationEvents =
    MutableSharedFlow<LoginEvent>()

val navigationEvents: SharedFlow<LoginEvent> =
    _navigationEvents
```

When login succeeds:

```kotlin
_navigationEvents.emit(
    LoginEvent.NavigateToHome
)
```

The UI observes the event and performs the navigation.

---

## State And Events

Notice the parallel with StateFlow.

```text
StateFlow

↓

Observe State

-------------------------

MutableStateFlow

↓

Observe

+

Update State
```

and

```text
SharedFlow

↓

Observe Events

-------------------------

MutableSharedFlow

↓

Observe

+

Emit Events
```

The ownership pattern is exactly the same.

Only the kind of information is different.

---

## Another Common Misconception

A common misconception is:

```text
SharedFlow And MutableSharedFlow Represent Different Streams
```

Incorrect.

They represent:

```text
The Same Event Stream
```

The only difference is:

```text
Can New Events Be Emitted?
```

---

## Another Common Misconception

A common misconception is:

```text
Every Consumer Needs MutableSharedFlow
```

Usually only the owner of the event stream should emit events.

Everyone else simply observes them.

---

## Putting Everything Together

```text
              Payment Events

                    │

          Payment Successful

                    ▲

                    │

        SharedFlow        MutableSharedFlow

        Observe           Observe + Emit
```

There is still:

```text
One Event Stream
```

The difference is simply:

```text
Permission
```

---

## The Key Mental Model

Think of a company announcement system.

```text
One Announcement System

↓

Employee

↓

Observe

-------------------------

One Announcement System

↓

Administrator

↓

Observe

+

Announce
```

Similarly:

```text
One Event Stream

↓

SharedFlow

↓

Observe

-------------------------

One Event Stream

↓

MutableSharedFlow

↓

Observe

+

Emit
```

The event stream remains the same.

Only the capability changes.

---

## Revision Table

| Section | Content |
|----------|----------|
| Core Idea | MutableSharedFlow represents the same event stream with emit capability |
| Mental Model | Company announcement system |
| SharedFlow | Observe |
| MutableSharedFlow | Observe + Emit |
| Android Examples | Payment events, Navigation events |
| Common Misconception | They do not represent different streams |
| Previous Concept | SharedFlow |
| Next Concept | Replay |
