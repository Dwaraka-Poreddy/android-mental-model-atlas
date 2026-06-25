# Composition Tree

## Observation

In the previous chapter, we learned that a screen is built by combining many smaller composable functions.

For example:

```kotlin
@Composable
fun LoginScreen() {

    Logo()

    WelcomeMessage()

    LoginForm()

    LoginButton()

}
```

To us,

this is simply a series of function calls.

Then an interesting question appears.

```text
What Happens After LoginScreen() Finishes?
```

A normal function executes...

returns...

and disappears.

```text
Function Executes

↓

Returns

↓

Gone
```

So another question naturally follows.

```text
How Does Compose Still Know That LoginButton() Belongs Inside LoginScreen()?
```

---

## A Thought Experiment

Imagine a company.

```text
CEO

├── Engineering

│   ├── Android

│   └── iOS

└── HR
```

Suppose every employee introduces themselves one by one.

Eventually, everyone finishes speaking.

The introductions are over.

But the company still knows:

- who reports to whom
- which team each person belongs to
- how everyone is connected

The conversations ended.

The hierarchy remained.

---

## Compose Faces The Same Problem

Composable functions execute.

Then they finish.

Yet Compose still needs to remember:

```text
LoginScreen

├── Logo

├── WelcomeMessage

├── LoginForm

└── LoginButton
```

Otherwise,

it would lose the structure of your UI every time a function returned.

---

## A New Mental Model

To solve this,

Compose builds its own representation of the UI.

That representation is called the:

```text
Composition Tree
```

Think of it as:

```text
Compose's Understanding Of Your UI
```

It is **not** the UI itself.

It is **not** the pixels on the screen.

It is Compose's internal representation of the UI you described.

---

## A Crucial Distinction

Notice how the responsibilities are divided.

```text
Developer

↓

Writes Composable Functions

-------------------------

Compose Runtime

↓

Builds Composition Tree
```

You describe the UI.

Compose remembers the structure.

Each has a different responsibility.

---

## Why Does Compose Need It?

Imagine the user types one character into the email field.

Compose already knows:

```text
LoginScreen

↓

LoginForm

↓

EmailField
```

It doesn't need to rediscover how your UI is organized.

It already has that information.

That allows Compose to make intelligent update decisions later.

We'll explore those decisions in the next chapter.

---

## What The Composition Tree Is NOT

The Composition Tree is **not**:

```text
The Screen

The Views

The Pixels

The Layout
```

It is simply:

```text
Compose's Internal Representation Of Your UI Structure
```

---

## Revision

### Core Idea

```text
Composition Tree = Compose's Understanding Of Your UI
```

### Mental Model

```text
Developer

↓

Composable Functions

-------------------------

Compose Runtime

↓

Composition Tree
```

### Production Recognition

When someone says:

```text
Composition Tree
```

think:

```text
The Structure Compose Remembers After Composable Functions Finish Executing
```

---

## Next Question

Compose now remembers the structure of the UI.

A natural question appears.

```text
What Happens When The State Changes?
```

Does Compose rebuild everything?

Or only part of the screen?

That leads us to the next concept.

```text
Recomposition
```
