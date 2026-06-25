# Imperative UI vs Declarative UI

## Observation

Suppose you are building a Login screen.

Initially:

```text
------------------------
 Email:    ____________

 Password: ____________

        [ Login ]
------------------------
```

The user starts typing:

```text
Email = "dwaraka@gmail.com"

Password = "secret123"
```

After validation:

```text
------------------------
 Email:    dwaraka@gmail.com

 Password: *********

      [ Login ]  ✅ Enabled
------------------------
```

A simple question appears.

```text
How Did The UI Change?
```

---

## The Traditional Approach

Imagine the UI is a whiteboard.

Initially, you draw:

```text
Email Box

Password Box

Disabled Button
```

Now the user types.

You manually erase and redraw parts of the screen.

```text
Enable Button

↓

Update Text

↓

Hide Error

↓

Show Loading
```

Every UI change requires explicit instructions.

```text
User Action

↓

Find View

↓

Update View

↓

Find Another View

↓

Update Another View
```

The developer is responsible for every UI update.

---

## A Thought Experiment

Imagine instead that you simply describe the screen.

```text
If Email Is Valid AND Password Is Valid

↓

Show Enabled Login Button

Else

↓

Show Disabled Login Button
```

You never say:

```text
Enable Button

Disable Button

Change Color
```

Instead, you simply describe what the screen should look like for the current situation.

---

## A New Mental Model

Instead of asking:

```text
How Do I Update The UI?
```

ask:

```text
How Do I Describe The UI?
```

That single shift changes everything.

---

## Imperative UI

Imperative UI tells the framework:

```text
Do This

↓

Then Do This

↓

Then Change This

↓

Then Hide That
```

The developer controls every UI update.

---

## Declarative UI

Declarative UI tells the framework:

```text
Given This State, The Screen Should Look Like This.
```

The framework decides how to update the actual UI.

---

## A Familiar Example

If you've worked with Flutter, you've already used this approach.

```dart
Widget build(BuildContext context) {
    return Text(counter.toString());
}
```

You never write:

```text
Find Text Widget

↓

Update Text
```

Instead, you simply describe:

```text
Current Counter

↓

Text Widget
```

Jetpack Compose follows the same philosophy.

---

## The Core Mental Model

```text
State

↓

UI Description

↓

Framework Updates UI
```

The developer describes.

The framework updates.

---
### Another Way To Think About It

The same idea is often written like this:

```text
UI = f(State)
```

Read it as:

```text
UI Is A Function Of State
```

This does **not** mean the UI is a mathematical function.

It simply means:

```text
Current State

↓

Current UI
```

If the state changes,

the UI description naturally changes as well.

Notice that this is exactly the same mental model we just learned.

```text
State

↓

UI Description

↓

Framework Updates UI
```

`UI = f(State)` is simply a shorter way of expressing it.

---
## Why Compose Exists

The traditional Android View system was primarily built around this style:

```text
Find View

↓

Modify View

↓

Repeat
```

Compose asks a different question.

```text
Can UI Simply Be A Function Of State?
```

If the state changes,

the UI description changes.

The framework handles the rest.

---

## Production Recognition

When you see:

```kotlin
Button(
    enabled = isLoginEnabled
)
```

don't think:

```text
Enable Button
```

think:

```text
Describe The Button For The Current State
```

You are not updating the button.

You are describing how the button should look.

---

## Revision

### Core Idea

```text
Imperative UI

↓

Tell The UI How To Change

-------------------------

Declarative UI

↓

Describe What The UI Should Look Like
```

### Mental Model

```text
State

↓

UI Description

↓

Framework Updates UI
```

### Production Recognition

```kotlin
Text(text = username)
```

↓

```text
Describe UI Don't Update UI
```

---

## Next Question

We now understand that Compose is about describing UI.

A natural question appears.

```text
What Exactly Are We Writing?
```

Are we writing screens?

Views?

Widgets?

Functions?

That leads us to the next concept:

```text
Composable
```
