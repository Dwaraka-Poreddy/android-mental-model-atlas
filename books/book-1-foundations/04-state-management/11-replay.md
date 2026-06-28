# Replay

## Looking Back

In the previous chapter we learned that:

```text
SharedFlow

↓

Shares Emissions With Multiple Collectors
```

A natural question appears.

Suppose a SharedFlow has already emitted several values.

A new collector starts observing it.

```text
What Should The New Collector Immediately Receive?
```

---

## A Simple Observation

Imagine the following values are emitted:

```text
A

↓

B

↓

C

↓

D

↓

E
```

Now a new collector starts collecting.

Should it receive:

```text
Nothing?
```

Or:

```text
E?
```

Or perhaps:

```text
C

↓

D

↓

E?
```

Different applications may require different behavior.

Replay exists to control exactly that.

---

## Replay

Replay defines:

```text
How Many Previous Emissions Should Be Immediately Given To A New Collector
```

Conceptually:

```text
Replay

↓

Previous Emissions

↓

New Collector
```

---

## Replay = 0

```kotlin
MutableSharedFlow<String>(
    replay = 0
)
```

Suppose the following values were emitted:

```text
A

↓

B

↓

C
```

A new collector starts collecting.

It immediately receives:

```text
Nothing
```

and waits for future emissions.

Conceptually:

```text
A

↓

B

↓

C

↓

New Collector

↓

Wait For Future Values
```

---

## Replay = 1

```kotlin
MutableSharedFlow<String>(
    replay = 1
)
```

Suppose the following values were emitted:

```text
A

↓

B

↓

C
```

A new collector starts collecting.

It immediately receives:

```text
C
```

and then continues receiving future emissions.

Conceptually:

```text
Remember The Last Emission

↓

Give It To New Collectors
```

---

## Replay = N

Replay is not limited to 0 or 1.

For example:

```kotlin
MutableSharedFlow<String>(
    replay = 3
)
```

Suppose the following values were emitted:

```text
A

↓

B

↓

C

↓

D

↓

E
```

The SharedFlow remembers:

```text
C

↓

D

↓

E
```

A new collector immediately receives:

```text
C

↓

D

↓

E
```

before continuing with future emissions.

Conceptually:

```text
Replay = N

↓

Remember The Last N Emissions
```

---

## Another Mental Model

Imagine a whiteboard.

Someone writes:

```text
Sprint Demo 3 PM
```

A new team member walks into the room.

Should they see:

```text
Sprint Demo 3 PM
```

or an empty whiteboard?

That depends entirely on whether the message was left on the board.

Replay answers the same question:

```text
How Much Previously Shared Information Should Still Be Visible To Someone Who Arrives Later?
```

---

## Android Example

Suppose a ViewModel exposes announcements.

```kotlin
private val _announcements =
    MutableSharedFlow<Announcement>(
        replay = 1
    )

val announcements: SharedFlow<Announcement> =
    _announcements
```

If:

```text
Maintenance Tonight 10 PM
```

was the latest announcement,

a new collector immediately receives:

```text
Maintenance Tonight 10 PM
```

without waiting for another announcement.

---

## Another Common Misconception

A common misconception is:

```text
Replay Can Only Be 0 Or 1
```

Incorrect.

Replay can be:

```text
0

1

2

3

...

N
```

representing:

```text
How Many Previous Emissions Should Be Immediately Delivered To A New Collector
```

---

## Another Common Misconception

A common misconception is:

```text
Replay Changes Future Emissions
```

It does not.

Future emissions behave exactly the same.

Replay only affects:

```text
What A New Collector Immediately Receives
```

---

## Putting Everything Together

```text
Replay = 0

↓

Remember Nothing

↓

New Collector Receives Nothing

--------------------------------

Replay = 1

↓

Remember Last Emission

↓

New Collector Receives Last Emission

--------------------------------

Replay = N

↓

Remember Last N Emissions

↓

New Collector Receives Last N Emissions
```

---

## The Key Mental Model

Replay answers one simple question:

```text
A New Collector Has Just Started.

How Many Previous Emissions Should It Immediately Receive?
```

Everything else naturally follows from that answer.

---

## Revision Table

| Section | Content |
|----------|----------|
| Core Idea | Replay controls what previous emissions a new collector immediately receives |
| Replay = 0 | Receive nothing |
| Replay = 1 | Receive the last emission |
| Replay = N | Receive the last N emissions |
| Mental Model | Whiteboard |
| Android Example | Shared announcements |
| Previous Concept | MutableSharedFlow |
| Next Concept | Channel |
