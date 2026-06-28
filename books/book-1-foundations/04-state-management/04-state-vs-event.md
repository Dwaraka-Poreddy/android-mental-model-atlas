# State vs Event

## A Simple Observation

Consider a music player.

It currently shows:

```text
Song:
Believer

Playing:
true

Progress:
01:42
```

Now imagine closing and reopening the screen.

What should you see?

```text
Song:
Believer

Playing:
true

Progress:
01:42
```

again.

This information still represents the application's current situation.

---

## Another Example

Consider a shopping cart.

```text
Items:
3

Total:
₹1250
```

Rotate the phone.

The screen should still display:

```text
Items:
3

Total:
₹1250
```

because this information is still true.

---

## One More Example

A payment succeeds.

The application shows:

```text
Payment Successful
```

Now rotate the phone.

Should the message appear again?

```text
No
```

because it was only meant to inform the user about something that already happened.

---

> **Side Note**
>
> In Android, actions such as rotating the device usually cause the current screen to be created again.
>
> For this discussion, you do not need to understand how or why that happens. Simply think of it as:
>
> ```text
> Build The Screen Again Using  Whatever Information The Application Currently Has
> ```
>
> We will explore the complete lifecycle and rotation behavior in a later document.

---

## Another Example

The user taps:

```text
Login
```

The application navigates to:

```text
Home Screen
```

Now rotate the phone.

Should navigation happen again?

Again:

```text
No
```

Navigation is an action that already happened.

---

## State vs Event

Applications deal with two different kinds of information.

Some information describes:

```text
What Is True

Right Now
```

Other information describes:

```text
Something

That Just Happened
```

The first is called:

```text
State
```

The second is called:

```text
Event
```

---

## State

State represents:

```text
The Current Situation
```

Examples:

```text
Current User

Current Song

Current Cart

Loading Status

Current Screen
```

If someone opens the screen now, they should immediately know this information.

---

## Event

An event represents:

```text
An Occurrence
```

Examples:

```text
Show Toast

Navigate To Home

Open Dialog

Payment Successful

Item Added
```

Once handled, the event is usually finished.

---

## Another Mental Model

Imagine watching a cricket match.

The scoreboard shows:

```text
130 / 3
```

This represents:

```text
The Current Match Situation
```

Now imagine the commentator shouting:

```text
SIX!!
```

The shout tells you:

```text
Something Just Happened
```

Five minutes later, you expect to see:

```text
130 / 3
```

on the scoreboard.

You do not expect the commentator to repeat:

```text
SIX!!
```

again.

Conceptually:

```text
Scoreboard

↓

State

-------------------

Commentary

↓

Event
```

---

## Android Example

A login screen may represent its current situation as:

```kotlin
data class LoginState(

    val username: String,

    val isLoading: Boolean,

    val isLoggedIn: Boolean
)
```

Every property answers:

```text
What Is True

Right Now?
```

---

A login screen may also produce one-time actions.

```kotlin
sealed interface LoginEvent {

    data object NavigateToHome : LoginEvent

    data class ShowToast(
        val message: String
    ) : LoginEvent
}
```

Each event answers:

```text
What Should

Happen Once?
```

---

## A Simple Decision Rule

Whenever you are unsure, ask:

```text
If Someone Opens

This Screen

Right Now,

Should They

Immediately See This?
```

If the answer is:

```text
Yes
```

it is usually:

```text
State
```

If the answer is:

```text
No
```

it is usually:

```text
Event
```

---

## Another Common Misconception

A common misconception is:

```text
Show Toast

Is State
```

Suppose the user rotates the device.

If the toast is stored as state, it may appear again even though nothing new happened.

This is usually undesirable.

---

## Another Common Misconception

A common misconception is:

```text
Navigation

Is State
```

If navigation is treated as state, recreating the screen may trigger navigation again.

Navigation is typically a one-time occurrence rather than the current situation.

---

## Putting Everything Together

```text
Current User

↓

State

-------------------

Navigate To Home

↓

Event

-------------------

Current Cart

↓

State

-------------------

Show Toast

↓

Event
```

The distinction is not based on the API being used.

It is based on the kind of information being represented.

---

## The Key Mental Model

Ask one simple question:

```text
Should Someone

Opening The Screen

Right Now

See This?
```

```text
Yes

↓

State
```

```text
No

↓

Event
```

This simple rule correctly classifies most information in Android applications.

---

## Revision Table

| Section | Content |
|----------|----------|
| State | Represents the current situation |
| Event | Represents something that happened |
| Mental Model | Scoreboard vs Commentary |
| Decision Rule | "Should someone opening the screen right now see this?" |
| Android Examples | LoginState, LoginEvent |
| Common Mistakes | Treating Toasts or Navigation as state |
| Previous Concept | Characteristics Of State |
| Next Concept | Why StateFlow Exists |
