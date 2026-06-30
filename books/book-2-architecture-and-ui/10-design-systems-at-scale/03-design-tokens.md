# Design Tokens

## Looking Back

In DOC 9 we defined colors inside `lightColorScheme()`

and read them through `MaterialTheme.colorScheme`.

We called that "theming."

But we left one question open.

> What value does `primary` actually hold?
> Who decides that, and where?

That decision is a design token.

---

## The Problem With Raw Values

Imagine a Compose file.

```kotlin
Box(
    modifier = Modifier.background(Color(0xFF2196F3))
)
```

This works.

But ask three questions:

```text
"What is this color?"
        ↓
A blue. Which blue? Unknown.

"Where else is it used?"
        ↓
No way to know without searching.

"Can I change it safely?"
        ↓
Search all files. Update each one. Hope nothing is missed.
```

Now compare:

```kotlin
Box(
    modifier = Modifier.background(MaterialTheme.colorScheme.primary)
)
```

```text
"What is this color?"
        ↓
The primary brand color.

"Where else is it used?"
        ↓
Everywhere primary is used — same answer everywhere.

"Can I change it safely?"
        ↓
Yes. Change one mapping. Propagates everywhere.
```

The difference is not the value.

The difference is the name.

---

## The Mental Model

```text
Design Token = A Named Value That Represents a Design Decision
```

Not `#2196F3`.

But `colorPrimary`.

Not `16.dp`.

But `spacingMedium`.

The name carries meaning.

The meaning makes the system maintainable.

---

## Why Named Values Matter

```text
Hardcoded                      Token-based
        ↓                              ↓
Color(0xFF1976D2)              MaterialTheme.colorScheme.primary
        ↓                              ↓
"What color is this?"          "This is the primary action color"
"Where else is it used?"       "Everywhere primary is used"
"Can I change it safely?"      "Yes — one file, propagates everywhere"
```

The raw value answers none of the important questions.

The token answers all of them.

---

## Two Levels of Tokens

Professional design systems use two levels.

```text
Level 1 — Raw Values
(never referenced directly in UI code)

    primary-50  = #E3F2FD
    primary-500 = #2196F3
    primary-900 = #0D47A1

                ↓

Level 2 — Semantic Tokens
(the only tokens UI code should reference)

    colorPrimary           = primary-500
    colorPrimaryContainer  = primary-50
    colorOnPrimary         = white
```

Notice the split.

Level 1 defines every possible shade.

Level 2 gives each shade a meaning:

"This is the primary action color."

"This is the background for a primary-tinted container."

"This is what text looks like on top of primary."

Feature code never touches Level 1.

It only ever asks: "What is the semantic color for this context?"

---

## Minimal Code

```kotlin
// Raw palette — defined once, never used in UI
object AppPalette {
    val Blue500 = Color(0xFF2196F3)
    val Blue50  = Color(0xFFE3F2FD)
    val White   = Color(0xFFFFFFFF)
}

// Semantic tokens — the only tokens UI references
object AppTokens {
    val ColorPrimary          = AppPalette.Blue500
    val ColorPrimaryContainer = AppPalette.Blue50
    val ColorOnPrimary        = AppPalette.White
}
```

Three lines define the raw palette.

Three lines give each raw value a meaning.

UI code never sees `Blue500`.

It sees `ColorPrimary`.

---

## Production Code

```kotlin
// tokens/AppPalette.kt
// TODO: Securely load brand colors from your design token source of truth.
// Do not hardcode production palette values here without a token pipeline.
internal object AppPalette {
    val Blue50  = Color(0xFFE3F2FD)
    val Blue500 = Color(0xFF2196F3)
    val Blue900 = Color(0xFF0D47A1)
    val White   = Color(0xFFFFFFFF)
    val Black   = Color(0xFF000000)
    val Grey100 = Color(0xFFF5F5F5)
    val Grey900 = Color(0xFF212121)
}

// tokens/AppTokens.kt
object AppTokens {
    val ColorPrimary          = AppPalette.Blue500
    val ColorPrimaryContainer = AppPalette.Blue50
    val ColorOnPrimary        = AppPalette.White
    val ColorBackground       = AppPalette.Grey100
    val ColorOnBackground     = AppPalette.Grey900
}

// theme/AppTheme.kt
fun appLightColorScheme() = lightColorScheme(
    primary          = AppTokens.ColorPrimary,
    primaryContainer = AppTokens.ColorPrimaryContainer,
    onPrimary        = AppTokens.ColorOnPrimary,
    background       = AppTokens.ColorBackground,
    onBackground     = AppTokens.ColorOnBackground,
)

// In any Composable — reads from MaterialTheme, not from raw values
Button(
    colors = ButtonDefaults.buttonColors(
        containerColor = MaterialTheme.colorScheme.primary
    )
)
```

### How To Read It

```text
internal object AppPalette
        ↓
The `internal` modifier matters.
Raw palette values are package-private.
Feature modules cannot import them.
They can only reach semantic tokens.

AppTokens.ColorPrimary = AppPalette.Blue500
        ↓
The semantic alias.
This is where the design decision lives:
"Blue500 is the primary brand color."

MaterialTheme.colorScheme.primary
        ↓
The standard Compose access point.
UI code reads here.
Changing appLightColorScheme() changes every usage.
```

---

## Spacing Tokens Follow the Same Pattern

```kotlin
object SpacingTokens {
    val XS  =  4.dp
    val S   =  8.dp
    val M   = 16.dp
    val L   = 24.dp
    val XL  = 32.dp
    val XXL = 48.dp
}
```

A designer says: "All section padding should be `M`."

Every developer knows what `M` means.

Every Composable that uses `SpacingTokens.M` gets updated

by changing one line.

Consistent spacing is what makes an app feel polished.

Token-based spacing is how you maintain it.

---

## Dark Mode Is Mostly a Token Problem

Dark mode confuses many developers.

The common assumption:

```text
Dark Mode = Invert the colors
```

The real model:

```text
Dark Mode = Different semantic token mappings

colorPrimary (light)  → Blue500
colorPrimary (dark)   → Blue200

colorBackground (light) → Grey100
colorBackground (dark)  → Grey900
```

The semantic tokens stay the same.

The raw values they point to change.

```kotlin
fun appDarkColorScheme() = darkColorScheme(
    primary          = AppPalette.Blue200,   // lighter for dark backgrounds
    background       = AppPalette.Grey900,
    onBackground     = AppPalette.Grey100,
)
```

Two color schemes. One set of semantic tokens.

Every component works in both modes

without a single conditional in feature code.

---

## Production Notes

- The two-level token system (raw → semantic) is the industry standard.
  Material Design 3 uses it.
  The Figma token ecosystem is built around it.

- Never use raw color values in Composables.
  Always go through semantic tokens via `MaterialTheme.colorScheme`.

- Mark `AppPalette` as `internal` so feature modules cannot reach it.
  Enforce the abstraction at the module boundary.

- Spacing tokens enforce visual rhythm.
  Consistent spacing is what makes an app feel designed rather than assembled.

- Dark mode becomes trivially simple once tokens are in place.
  Without tokens, dark mode means touching every screen.

---

## Common Misconception

> **"I already use `MaterialTheme.colorScheme.primary` — that IS a token."**

Yes, but it's not enough.

Reading from `MaterialTheme.colorScheme.primary` is correct.

But someone has to decide what value maps to `primary`.

That mapping — `primary = AppPalette.Blue500` — is the token system.

Without the mapping layer, `primary` is still just a hardcoded value.

It just lives in one place instead of forty.

The full system is: named raw value → named semantic alias → MaterialTheme.

---

## A Natural Question

Tokens handle values.

What about reusable UI components?

How do we ensure every button looks the same

across ten feature teams?

> **Next: Component API Design**

---

## Revision

### Core Idea

```text
Design Token = Named Value + Design Intent

Raw value   → what the color IS
Token name  → what the color MEANS
```

### Mental Model

```text
AppPalette   (raw values, internal)
        ↓
AppTokens    (semantic names, exported)
        ↓
ColorScheme  (MaterialTheme slot)
        ↓
Composables  (read from MaterialTheme)
```

### Engineering Mindset

Don't think:

> **"What hex value should I use here?"**

Think:

> **"What does this color represent in the design language?
>    Is there a semantic token for that role?"**

### Key Insight

Tokens don't just help with consistency.

They make design intent readable in code.

A developer reading `colorPrimaryContainer`

knows more than a developer reading `Color(0xFFE3F2FD)`.

Readable intent is maintainable code.
