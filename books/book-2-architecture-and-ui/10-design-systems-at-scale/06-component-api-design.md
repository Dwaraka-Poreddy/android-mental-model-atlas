# Component API Design

## Looking Back

In DOC 9 we learned about reusable composables

and how to use the slot pattern to make them flexible.

In DOC 7 we saw how Compose's function model

makes components easy to compose and decompose.

We now have design tokens that give us consistent values.

But values alone don't prevent inconsistency.

If every team imports `MaterialTheme.colorScheme.primary` correctly,

they still write their own button.

And their buttons diverge.

---

## The Problem Without a Component Library

Without shared components,

each feature team builds their own.

```text
Feature A team builds:
    AuthPrimaryButton.kt
        Blue, filled, 8dp corner radius

Feature B team builds:
    HomeActionButton.kt
        Blue, filled, 6dp corner radius

Feature C team builds:
    ProfileSaveButton.kt
        Blue, filled, square corners
```

All three use the same token.

All three look different.

Design update: "All buttons should have 12dp corner radius."

```text
Update AuthPrimaryButton.kt
Update HomeActionButton.kt
Update ProfileSaveButton.kt
        ↓
Miss one
        ↓
Inconsistency
```

The token system solved the color problem.

The component library solves the structure problem.

---

## The Mental Model

```text
Component Library = A Set of Building Blocks
                    With Intentional API Surfaces

You control what callers CAN configure.
You enforce what they CANNOT.
```

This is the critical insight.

A good component is deliberately restrictive.

It doesn't let callers change the things

that define brand consistency.

---

## What to Expose, What to Enforce

```text
Expose to callers            Enforce in the component
        ↓                              ↓
Content (text, icon)          Background color
onClick action                Corner radius
enabled state                 Font size
loading state                 Internal padding
size variant                  Border behavior
```

Callers provide content and behavior.

The component enforces brand consistency.

---

## Minimal Code

```kotlin
@Composable
fun AppPrimaryButton(
    text: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    enabled: Boolean = true
) {
    Button(
        onClick = onClick,
        modifier = modifier,
        enabled = enabled,
        colors = ButtonDefaults.buttonColors(
            containerColor = MaterialTheme.colorScheme.primary
        )
    ) {
        Text(text)
    }
}
```

Four parameters.

No color parameter.

No corner radius parameter.

No font parameter.

Callers choose what to say and what happens on click.

The button chooses how it looks.

---

## Production Code

```kotlin
@Composable
fun AppPrimaryButton(
    text: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    enabled: Boolean = true,
    isLoading: Boolean = false
) {
    Button(
        onClick = onClick,
        modifier = modifier,
        enabled = enabled && !isLoading,
        colors = ButtonDefaults.buttonColors(
            containerColor = MaterialTheme.colorScheme.primary,
            disabledContainerColor = MaterialTheme.colorScheme.surfaceVariant
        ),
        shape = AppShapes.medium
    ) {
        if (isLoading) {
            CircularProgressIndicator(
                modifier = Modifier.size(16.dp),
                color = MaterialTheme.colorScheme.onPrimary,
                strokeWidth = 2.dp
            )
        } else {
            Text(
                text = text,
                style = MaterialTheme.typography.labelLarge
            )
        }
    }
}
```

### How To Read It

```text
containerColor = MaterialTheme.colorScheme.primary
        ↓
Uses semantic token, not a hardcoded color.
When the design system updates its primary color,
this button updates automatically.
Callers cannot override this.

shape = AppShapes.medium
        ↓
Uses a shape token.
Corner radius is defined in one place.
"Make all buttons 12dp" means changing AppShapes.medium.
Zero callers need to change.

enabled && !isLoading
        ↓
Loading implies disabled.
Callers don't manage this invariant themselves.
The component enforces it.

isLoading: Boolean
        ↓
The component owns the loading appearance.
Callers say "it's loading."
They never say "show a spinner sized 16dp with 2dp strokeWidth."
```

---

## The Contract

Think of every component as having a contract.

```text
AppPrimaryButton("Save", onClick = { viewModel.save() })

Caller provides:
    - What the button says
    - What happens on click
    - Whether it's loading
    - Whether it's enabled
    - Where it goes (modifier)

AppPrimaryButton enforces:
    - Brand color
    - Disabled color
    - Corner radius
    - Loading appearance
    - Typography
    - Disabled behavior during loading
```

A caller who uses `AppPrimaryButton`

gets all of the second list for free.

They cannot accidentally break it.

---

## The Slot Pattern for Flexible Components

Some components need flexible content.

The slot pattern from DOC 7 applies here.

```kotlin
@Composable
fun AppCard(
    modifier: Modifier = Modifier,
    content: @Composable ColumnScope.() -> Unit
) {
    Card(
        modifier = modifier,
        shape = AppShapes.large,
        colors = CardDefaults.cardColors(
            containerColor = MaterialTheme.colorScheme.surface
        ),
        elevation = CardDefaults.cardElevation(defaultElevation = 2.dp)
    ) {
        Column(
            modifier = Modifier.padding(SpacingTokens.M),
            content = content
        )
    }
}
```

Usage:

```kotlin
AppCard {
    Text("Title", style = MaterialTheme.typography.titleMedium)
    Text("Subtitle", style = MaterialTheme.typography.bodySmall)
}
```

The card enforces shape, color, elevation, and padding.

The caller provides any content they want inside.

One card component.

Used across the entire app.

---

## Component Organization

A component library is a dedicated module.

```text
core/
    designsystem/
        src/
            components/
                button/
                    AppPrimaryButton.kt
                    AppSecondaryButton.kt
                    AppTextButton.kt
                card/
                    AppCard.kt
                input/
                    AppTextField.kt
            tokens/
                AppPalette.kt
                AppTokens.kt
                AppShapes.kt
                AppTypography.kt
            theme/
                AppTheme.kt
```

Feature modules depend on `core:designsystem`.

They import components by name.

They never define their own colors or shapes.

---

## Compose Previews as Living Documentation

Each component should have a `@Preview`.

```kotlin
@Preview(showBackground = true)
@Composable
private fun AppPrimaryButtonPreview() {
    AppTheme {
        Column(
            modifier = Modifier.padding(SpacingTokens.M),
            verticalArrangement = Arrangement.spacedBy(SpacingTokens.S)
        ) {
            AppPrimaryButton("Save", onClick = {})
            AppPrimaryButton("Saving...", onClick = {}, isLoading = true)
            AppPrimaryButton("Disabled", onClick = {}, enabled = false)
        }
    }
}
```

Previews serve as documentation.

New developers open the preview file,

see every variant rendered,

and know how to use the component.

No written docs needed.

---

> **You'll see this in...**
> - **DOC 16 — Testing**, where component tests use `ComposeTestRule`
>   to test `AppPrimaryButton` in isolation without a ViewModel.
>   Clean component boundaries make testing straightforward.
> - **DOC 17 — Performance**, where component-level stability annotations
>   prevent unnecessary recomposition.
>   A stable `AppPrimaryButton` won't recompose unless its parameters change.

---

## Production Notes

- Component API = what you expose via parameters.
  Be deliberate.
  Every parameter is a commitment.
  Adding parameters is easy; removing them breaks callers.

- `modifier: Modifier = Modifier` should always be the third parameter.
  This is the Compose convention.
  It allows callers to control size and position
  without breaking internal layout decisions.

- Slot APIs (`content: @Composable () -> Unit`) are for components
  that need flexible inner content.
  Use them for containers, cards, and dialogs.
  Avoid them for atomic components like buttons.

- Test components in isolation with Compose previews
  and `ComposeTestRule`.
  Never test a component through a ViewModel.

- A component that needs a ViewModel is not a component.
  It's a feature.
  Split it: put data in a ViewModel,
  pass it down to a pure composable component.

---

## Common Misconception

> **"Components should be maximally flexible."**

The opposite is true.

A good component is deliberately restrictive.

Flexibility means callers can break brand consistency.

Restrictiveness means they cannot.

The goal is not to handle every case.

The goal is to handle the design system's cases

and make other cases impossible.

---

## A Natural Question

How do feature teams know what components exist?

How is a component library organized

when it grows to fifty or a hundred components?

How do you communicate breaking changes

to ten teams using the same components?

> **That's component library architecture —
>    the organization and governance side of design systems.**

---

## Revision

### Core Idea

```text
Component Library = Building Blocks With Intentional APIs

Callers provide: content and behavior
Components enforce: visual consistency
```

### Mental Model

```text
Feature Team writes:
    AppPrimaryButton("Save", onClick = { viewModel.save() })

Design System enforces:
    Color, shape, typography, loading behavior,
    disabled behavior, accessibility semantics
```

### Engineering Mindset

Don't think:

> **"What parameters should I expose so callers can do anything?"**

Think:

> **"What does a caller need to provide?
>    What should they never need to touch?"**

Expose the minimum.

Enforce the rest.

### Key Insight

A component's value comes from what it hides,

not from what it exposes.

Every hidden implementation detail

is one fewer inconsistency

waiting to happen across ten feature teams.
