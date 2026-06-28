# Composable

## Observation

In the previous chapter, we learned:

```text
State

↓

UI Description

↓

Framework Updates UI
```

That's a beautiful idea.

But another question immediately appears.

```text
How Do We Actually Describe The UI?
```

Do we write:

```text
XML?
```

Or

```text
Views?
```

Or

```text
Layouts?
```

Or something entirely different?

---

## A Thought Experiment

Suppose I ask you to describe this screen.

```text
------------------------

Welcome, Dwaraka

[ Login ]

------------------------
```

One way is to draw it.

Another way is to describe it.

```text
Show

A Welcome Message

↓

Then

A Login Button
```

Notice something.

You're describing **what** the screen contains,

not **how** it should be drawn.

---

## What If UI Were Just A Function?

Think about a normal Kotlin function.

```kotlin
fun greet(name: String): String {
    return "Welcome $name"
}
```

Input:

```text
"Dwaraka"
```

↓

Output:

```text
"Welcome Dwaraka"
```

A function takes an input,

produces an output,

and finishes.

Now imagine a function whose output isn't text.

Its output is...

```text
UI
```

---

## A New Mental Model

A Composable is simply:

```text
A Function That Describes A Piece Of UI
```

A regular function exists to produce a value.

A composable exists to describe a piece of UI.

That is the only mental shift you need.

Compose simply uses functions for a different purpose.

---

## Why A Function?

Functions already give us everything we need.

They can:

```text
Accept Inputs

↓

Reuse Logic

↓

Be Combined

↓

Be Tested
```

Instead of inventing a brand-new language for UI,

Compose builds UI using one of the most familiar concepts in programming:

```text
Functions
```

---

## Building Bigger Screens

One function can describe:

```text
Welcome Message
```

Another can describe:

```text
Login Form
```

Another can describe:

```text
Login Button
```

Together:

```text
Welcome()

↓

LoginForm()

↓

LoginButton()
```

A complete screen starts to emerge.

Large screens are simply built by combining smaller UI functions.

---

## Production Recognition

When you see:

```kotlin
@Composable
fun LoginScreen() {
    ...
}
```

don't think:

```text
Special Android Class
```

think:

```text
A Function That Describes Part Of The UI
```

The `@Composable` annotation simply tells Compose:

> This function participates in building the UI.

How Compose makes that possible is something we'll learn later.

For now, focus only on the mental model.

---

## Revision

### Core Idea

```text
Composable = A Function That Describes A Piece Of UI
```

### Mental Model

```text
Small UI Functions

↓

Combined Together

↓

Complete Screen
```

### Production Recognition

```kotlin
@Composable
fun LoginScreen()
```

↓

```text
Function That Describes UI
```

---

## Next Question

We now know that UI is built from composable functions.

A natural question appears.

```text
How Can Multiple Composable Functions Work Together To Build One Screen?
```

That leads us to the next concept.

```text
Composition
```
