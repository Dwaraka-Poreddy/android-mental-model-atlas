# Composition

## Observation

In the previous chapter, we learned:

```text
Composable = A Function That Describes A Piece Of UI
```

A simple screen might have just one composable.

```kotlin
@Composable
fun Welcome() { ... }
```

But real applications rarely consist of a single piece of UI.

A login screen might contain:

```text
Logo

↓

Welcome Message

↓

Email Field

↓

Password Field

↓

Login Button
```

A natural question appears.

```text
How Do These Independent Composable Functions Become One Screen?
```

---

## A Thought Experiment

Imagine asking five people to build one part of a Lego house.

Person 1 builds:

```text
Roof
```

Person 2 builds:

```text
Door
```

Person 3 builds:

```text
Windows
```

Person 4 builds:

```text
Walls
```

Each person creates one independent piece.

Eventually,

those pieces are assembled into:

```text
One House
```

Notice something.

Nobody builds the entire house.

Each person builds one piece.

The complete house emerges by combining those pieces together.

---

## Building A Screen

Compose encourages exactly the same way of thinking.

Instead of writing one enormous function:

```kotlin
@Composable
fun LoginScreen() {
    // Hundreds of lines...
}
```

we divide the screen into smaller responsibilities.

```kotlin
@Composable
fun LoginScreen() {

    Logo()

    WelcomeMessage()

    LoginForm()

    LoginButton()

}
```

Each composable describes one piece of the screen.

The screen is built by combining those smaller pieces.

---

## A New Mental Model

Composition is simply:

```text
Building A Larger UI By Combining Smaller Composables
```

Nothing more.

Nothing less.

Composition is something **you**, as a developer, do while designing your UI.

---

## Why Is This Useful?

Smaller composables are easier to:

```text
Read

↓

Reuse

↓

Modify

↓

Test
```

Instead of thinking:

```text
One Giant Screen
```

think:

```text
Many Small Pieces

↓

One Screen
```

---

## Building Larger Pieces

Imagine these composables.

```text
Avatar()

Name()

Status()
```

Together they form:

```text
ProfileHeader()
```

Now imagine:

```text
ProfileHeader()

Posts()

BottomBar()
```

Together they form:

```text
ProfileScreen()
```

Composition happens at every level.

Small pieces become larger pieces.

Larger pieces become complete screens.

---

## Production Recognition

When you see:

```kotlin
@Composable
fun HomeScreen() {

    Toolbar()

    Banner()

    Categories()

    ProductGrid()

}
```

don't think:

```text
One Huge Screen
```

think:

```text
Small UI Pieces

↓

Combined Into One Screen
```

This is composition.

---

## What Composition Does NOT Mean

Composition does **not** mean:

```text
Drawing

Rendering

Displaying
```

Those are responsibilities of the Compose runtime.

Composition simply means:

```text
Building A Larger UI From Smaller Composables
```

It is the architectural act of structuring your UI.

---

## Revision

### Core Idea

```text
Composition

=

Building A Larger UI By Combining Smaller Composables
```

### Mental Model

```text
Small UI Pieces

↓

Larger UI Pieces

↓

Complete Screen
```

### Production Recognition

```kotlin
LoginScreen() {

    Logo()

    LoginForm()

    LoginButton()

}
```

↓

```text
Compose Small UI Pieces Into One Screen
```

---

## Next Question

We now understand how **we** build a screen.

But another question naturally appears.

```text
When We Combine Composable Functions, How Does Compose Understand Their Structure?
```

Or, in other words:

```text
How Does Compose Represent The UI We Built?
```

That leads us to the next concept.

```text
Composition Tree
```
