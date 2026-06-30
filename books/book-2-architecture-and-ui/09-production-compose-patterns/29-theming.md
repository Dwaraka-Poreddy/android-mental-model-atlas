# Theming

## Observation

In the previous two chapters,

we learned two important ideas.

First,

a Design System defines the application's shared design decisions.

```text
Design System

↓

Colors

Typography

Shapes

Spacing

...
```

Then,

we learned that `CompositionLocal` allows shared values to flow through the Composition Tree.

A natural question appears.

> **How does Jetpack Compose combine these two ideas to build a real application?**

The answer is:

**Theming.**

---

## What Is A Theme?

Imagine a Design System says:

- Primary Color
- Secondary Color
- Error Color
- Title Typography
- Body Typography
- Rounded Shapes

These are decisions.

But they still need to become actual values inside the application.

A **Theme** is simply one concrete implementation of those design decisions.

For example,

our Design System might define:

```text
Primary Color
```

A Light Theme could implement it as:

```text
Blue
```

A Dark Theme could implement it as:

```text
Light Blue
```

Notice something.

The Design System didn't change.

Only the implementation changed.

---

## Design System vs Theme

These two concepts are closely related,

but they're not the same thing.

Think of it like this.

```text
Design System

↓

Defines

What Decisions Exist

-----------------------

Theme

↓

Provides

Concrete Values
```

The Design System might say:

```text
Every application has:

• Primary Color
• Secondary Color
• Title Typography
• Shapes
```

The Theme answers:

```text
Primary = Blue

Secondary = Green

Title = 24sp Bold

Shape = Rounded
```

One defines the concepts.

The other supplies the values.

---

## This Should Feel Familiar

A natural question appears.

> **How do these values reach every composable?**

We've already answered that question.

```text
CompositionLocal
```

Compose doesn't pass colors,

typography,

or shapes through every composable.

Instead,

it distributes them through the Composition Tree.

The Theme is simply a collection of values

provided using `CompositionLocal`.

---

## MaterialTheme

Compose provides a ready-made implementation called:

```kotlin
MaterialTheme
```

Conceptually,

it looks something like this.

```kotlin
MaterialTheme(

    colorScheme = ...,

    typography = ...,

    shapes = ...

) {

    HomeScreen()

}
```

Notice something.

This should immediately remind you of:

```kotlin
CompositionLocalProvider(...)
```

That's because,

internally,

`MaterialTheme` uses `CompositionLocal`

to make these values available throughout the Composition Tree.

The API is different.

The underlying mechanism is the same.

---

## Reading Theme Values

Imagine we're deep inside the UI.

```text
App

↓

HomeScreen

↓

BalanceCard

↓

BalanceText
```

Inside `BalanceText`,

we simply write:

```kotlin
MaterialTheme.colorScheme.primary
```

or

```kotlin
MaterialTheme.typography.titleLarge
```

We never pass these values as parameters.

They are already available.

Why?

Because `MaterialTheme`

has already provided them to the Composition Tree.

---

## Light Theme And Dark Theme

Imagine our application supports two themes.

```text
Light Theme

↓

Primary = Blue
```

```text
Dark Theme

↓

Primary = Light Blue
```

Now imagine this composable.

```kotlin
Text(

    text = "Balance",

    color = MaterialTheme.colorScheme.primary

)
```

Did we change this composable?

No.

The code remains identical.

Only the Theme changes.

As a result,

the entire application automatically adopts the new colors,

typography,

and shapes.

That's one of the biggest advantages of theming.

---

## More Than Colors

Many developers think a Theme only contains colors.

In reality,

it often includes:

- Color Scheme
- Typography
- Shapes
- Elevation
- Motion
- Dimensions
- Spacing
- Icons

Anything that represents a shared design decision

can become part of the Theme.

---

## Thinking Like A Production Engineer

Whenever you're writing a composable,

don't ask:

> **Which color should I use?**

Instead ask:

> **Which semantic color from the Theme represents this purpose?**

For example,

instead of thinking:

```text
Blue
```

think:

```text
Primary
```

Instead of:

```text
Red
```

think:

```text
Error
```

The Theme provides meaning,

not merely values.

This makes the application much easier to redesign in the future.

---

## Connecting Everything Together

Notice how the previous chapters now fit together.

```text
Design System

↓

Defines

Shared Design Decisions

↓

Theme

↓

Provides Concrete Values

↓

CompositionLocal

↓

Distributes Those Values

↓

Composable

↓

MaterialTheme.colorScheme.primary
```

Every layer has a different responsibility.

Together,

they create a consistent application.

---

## Beyond Compose

This idea isn't unique to Compose.

Flutter has:

```dart
Theme.of(context)
```

SwiftUI has:

```text
Environment
```

React has:

```text
Context
```

Different frameworks.

Same idea.

Provide shared design values once,

allow every UI element to read them.

---

## Bringing Everything Together

```text
Design System

↓

Defines Decisions

↓

Theme

↓

Provides Values

↓

CompositionLocal

↓

Distributes Values

↓

Composable Reads

↓

Consistent UI
```

Notice something.

The composable doesn't know:

- whether it's Light Theme,
- Dark Theme,
- Brand A,
- Brand B.

It simply asks:

> **"What's the current primary color?"**

The Theme answers that question.

---

## Revision

### Core Idea

A Theme is a concrete implementation of a Design System.

It provides colors,

typography,

shapes,

and other shared design values to the Composition Tree.

---

### Mental Model

```text
Design System

↓

What Should Exist

↓

Theme

↓

What Values Should Be Used

↓

CompositionLocal

↓

How Those Values Reach Every Composable
```

---

### Engineering Mindset

Don't think:

> **"Themes are just colors."**

Think:

> **"A Theme is how an application provides shared design decisions to every composable."**

---

### Key Insight

`MaterialTheme` isn't a special language feature.

It's a well-designed abstraction built on top of `CompositionLocal`.

Understanding `CompositionLocal`

means you already understand how theming works internally.

---

## One Remaining Question

We've now learned how large applications organize their screens,

reuse components,

share design decisions,

and distribute themes.

A natural question appears.

> **As these applications continue growing, where should all these screens, components, themes, and design systems live inside the project?**

That leads us to the final concept of this document.

**Folder Structure**
