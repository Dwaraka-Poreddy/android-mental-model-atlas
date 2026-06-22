# Channel

## Looking Back

In the previous chapters we learned that:

```text
SharedFlow

↓

Shares Emissions With Multiple Collectors
```

A single emission may be observed by many interested collectors.

A natural question appears:

```text
What If A Value Is Intended For Only One Consumer?
```

---

## A Simple Observation

Imagine an application generates an OTP.

```text
Your OTP 482913
```

Who should receive it?

```text
Login Screen
```

Should every collector in the application receive the OTP?

```text
No
```

The OTP has one intended destination.

Conceptually:

```text
One Producer

↓

One Consumer
```

Some information is naturally delivered rather than shared.

---

## Channel

A Channel represents a stream where each value is intended to be:

```text
Delivered

To

One Consumer
```

Conceptually:

```text
Producer

↓

Channel

↓

Consumer
```

Once a value is received, it is not delivered again to another consumer.

---

## Another Mental Model

Imagine sending a parcel.

The sender ships:

```text
Parcel

↓

Flat 204
```

The courier delivers it.

After delivery:

```text
Parcel

↓

Delivered
```

The same parcel is not delivered to:

```text
Flat 205

Flat 206

Flat 207
```

There is:

```text
One Parcel

↓

One Receiver
```

A Channel behaves in a similar way.

---

## Android Example

Suppose a ViewModel needs to send navigation commands.

```kotlin
sealed interface NavigationEvent {

    data object OpenPayment : NavigationEvent

}
```

The ViewModel owns:

```kotlin
private val navigationChannel =
    Channel<NavigationEvent>()
```

When the user proceeds to payment:

```kotlin
navigationChannel.send(
    NavigationEvent.OpenPayment
)
```

A consumer receives the event:

```kotlin
val event = navigationChannel.receive()
```

The event is delivered to a single consumer.

---

## SharedFlow And Channel

Both can represent events.

The difference is in how those events are delivered.

```text
SharedFlow

↓

One Emission

↓

Many Collectors

--------------------------------

Channel

↓

One Emission

↓

One Consumer
```

SharedFlow emphasizes:

```text
Sharing
```

Channel emphasizes:

```text
Delivery
```

---

## Another Example

Suppose an event is emitted:

```text
Payment Successful
```

Using SharedFlow:

```text
Payment Successful

        │

        ├────────► UI

        ├────────► Analytics

        └────────► Logger
```

Using Channel:

```text
Payment Successful

        │

        ▼

One Consumer
```

The same event is not delivered to multiple independent collectors.

---

## Another Common Misconception

A common misconception is:

```text
Channel Is SharedFlow With One Collector
```

They represent different communication patterns.

```text
SharedFlow

↓

Share

-------------------------

Channel

↓

Deliver
```

---

## Another Common Misconception

A common misconception is:

```text
Every Event Should Use Channel
```

Not necessarily.

If multiple parts of an application should react to the same event, SharedFlow is often a more natural representation.

Channel is appropriate when each value is intended for:

```text
Exactly One Consumer
```

---

## Putting Everything Together

```text
SharedFlow

↓

One Emission

↓

Many Collectors

--------------------------------

Channel

↓

One Emission

↓

One Consumer
```

The distinction is not about syntax.

It is about:

```text
How The Value Is Intended To Be Consumed
```

---

## The Key Mental Model

Think of a parcel delivery.

```text
One Parcel

↓

One Receiver
```

Similarly:

```text
One Emission

↓

Channel

↓

One Consumer
```

Unlike SharedFlow, the emphasis is not on sharing the same emission with multiple collectors.

It is on delivering a value to its intended consumer.

---

## Revision Table

| Section | Content |
|----------|----------|
| Core Idea | Channel delivers each value to one consumer |
| Mental Model | Parcel delivery |
| Represents | One producer → one consumer communication |
| Android Example | Navigation events |
| SharedFlow | One emission → many collectors |
| Channel | One emission → one consumer |
| Previous Concept | Replay |
| Next Concept | Producer & Consumer |
