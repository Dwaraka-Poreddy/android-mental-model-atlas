# State

## A Simple Observation

Open a music player.

You immediately see:

```text
Song: Believer

Status: Playing

Progress: 01:42

Volume: 70%
```

One second later:

```text
Song: Believer

Status: Playing

Progress: 01:43

Volume: 70%
```

Another second later:

```text
Song: Believer

Status: Paused

Progress: 01:43

Volume: 70%
```

A simple observation appears:

```text
At Every Moment The Application Has Exactly One Current Situation
```

---

## Another Example

Open a weather application.

You see:

```text
Location: Hyderabad Temperature: 31°C

Condition: Sunny
```

Ten minutes later:

```text
Location: Hyderabad

Temperature: 32°C

Condition: Sunny
```

Again we observe:

```text
The Application Always Has One Current Situation
```

---

## Another Example

Consider a login screen.

Initially:

```text
Username: dwaraka

Password: ********

Loading: false
```

The user taps:

```text
Login
```

Now the screen becomes:

```text
Username: dwaraka

Password: ********

Loading: true
```

After a successful login:

```text
Logged In: true

Loading: false
```

At every moment the screen has one current situation.

---

## State

The current situation of an application at a particular moment is called:

```text
State
```

State answers the question:

```text
What Does The Application Look Like Right Now?
```

It does not describe:

```text
How We Reached Here
```

It simply describes:

```text
The Current Situation
```

---

## Another Mental Model

Imagine taking a photograph.

At:

```text
10:00 AM
```

you capture:

```text
Playing 01:42 70%
```

Take another photograph at:

```text
10:01 AM
```

and you capture:

```text
Paused 01:43 70%
```

Each photograph is:

```text
One Snapshot Of Reality
```

Applications work in the same way.

At every instant they have:

```text
One Snapshot Of Their Current Situation
```

That snapshot is:

```text
State
```

---

## Android Example

Suppose we are building a music player.

We might represent its current state as:

```kotlin
data class PlayerState(

    val songName: String,

    val isPlaying: Boolean,

    val progressSeconds: Int,

    val volume: Int
)
```

At one moment it may contain:

```kotlin
PlayerState(

    songName = "Believer",

    isPlaying = true,

    progressSeconds = 102,

    volume = 70
)
```

One second later:

```kotlin
PlayerState(

    songName = "Believer",

    isPlaying = true,

    progressSeconds = 103,

    volume = 70
)
```

The object changes because:

```text
The Current Situation Has Changed
```

---

## Another Android Example

Consider a login screen.

```kotlin
data class LoginState(

    val username: String,

    val password: String,

    val isLoading: Boolean,

    val isLoggedIn: Boolean
)
```

Initially:

```kotlin
LoginState(

    username = "",

    password = "",

    isLoading = false,

    isLoggedIn = false
)
```

While logging in:

```kotlin
LoginState(

    username = "dwaraka",

    password = "********",

    isLoading = true,

    isLoggedIn = false
)
```

After success:

```kotlin
LoginState(

    username = "dwaraka",

    password = "********",

    isLoading = false,

    isLoggedIn = true
)
```

Each object represents one snapshot of the screen.

---

## Another Android Example

Suppose we have a shopping cart.

```kotlin
data class CartState(

    val itemCount: Int,

    val totalPrice: Double,

    val isCheckoutEnabled: Boolean
)
```

Whenever the user adds or removes items, the current state changes.

At every moment there is exactly one:

```text
Current Cart State
```

---

## Another Common Misconception

A common misconception is:

```text
State Means Data That Never Changes
```

Incorrect.

State changes all the time.

What makes it state is:

```text
It Represents The Current Situation At This Moment
```

---

## Another Common Misconception

A common misconception is:

```text
State Represents The History Of The Application
```

Incorrect.

State represents:

```text
Now
```

not:

```text
Past Or Future
```

---

## The Key Mental Model

Applications continuously change.

However, at every instant they have exactly one:

```text
Current Situation
```

Conceptually:

```text
Current Situation

↓

Snapshot

↓

State
```

State does not describe:

```text
How The Application Changed
```

It describes:

```text
How The Application Looks Right Now
```

---

## Revision Table

| Section | Content |
|----------|----------|
| Core Idea | State represents the current situation |
| Mental Model | Snapshot / Photograph |
| Android Examples | Music Player, Login Screen, Shopping Cart |
| State Represents | Current situation at one moment |
| State Does Not Represent | History or future |
| Previous Document | Reactive Streams & Flow |
| Next Concept | State Holder |
