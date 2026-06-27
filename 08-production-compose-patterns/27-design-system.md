# Design System

## Observation

Imagine two Android developers.

One builds the Profile screen.

Another builds the Checkout screen.

Neither talks to the other.

A week later,

the application looks like this.

```text
Profile

Blue Button

16dp Padding

Rounded Corners

────────────────────

Checkout

Green Button

20dp Padding

Different Corners
```

Nothing is technically wrong.

Every screen works.

A natural question appears.

> **Why does the application feel inconsistent?**

---

## The Real Problem

Each engineer made perfectly reasonable decisions.

One chose:

```text
16dp
```

Another chose:

```text
20dp
```

One used:

```text
Blue
```

Another preferred:

```text
Green
```

One selected:

```text
Roboto Medium
```

Another selected:

```text
Roboto Bold
```

Individually,

none of these choices are terrible.

Collectively,

they create a product that feels inconsistent.

---

## A Bigger Insight

Notice something.

The problem isn't colors.

The problem isn't typography.

The real problem is **decision making**.

Every screen is making the same design decisions independently.

That should immediately feel familiar.

Earlier in the Atlas,

we learned about the **Single Source of Truth**.

The same principle appears again.

Only this time,

it applies to design.

---

## A Design System Is A Single Source Of Truth

Instead of every screen deciding:

- colors,
- typography,
- spacing,
- corner radius,
- buttons,
- elevations,
- icons,

we decide those things once.

Every screen simply reuses those decisions.

```text
Design System

↓

Colors

Typography

Spacing

Icons

Components

↓

Entire Application
```

Now,

the entire product speaks the same visual language.

---

## More Than A Collection Of Components

Many developers think a Design System is simply:

```text
Buttons

Cards

TextFields
```

Those are certainly part of it.

But they're only one piece.

A complete Design System also defines:

- colors,
- typography,
- spacing,
- corner radius,
- elevations,
- icons,
- motion,
- interaction behavior,
- accessibility guidelines.

Components are simply one expression of the system.

The real goal is consistency.

---

## Standardizing Decisions

The biggest value of a Design System isn't that it standardizes pixels.

It standardizes decisions.

Instead of asking:

> **Which blue should I use?**

Developers ask:

> **Which semantic color represents a primary action?**

Instead of asking:

> **Should this padding be 18dp?**

They ask:

> **Which spacing value from the design system applies here?**

Notice the difference.

We're no longer inventing values.

We're selecting shared concepts.

---

## Design Tokens

This is where design tokens enter the picture.

Instead of scattering raw values throughout the application,

the Design System gives those values meaningful names.

Instead of writing:

```text
#0A84FF
```

we define:

```text
Primary
```

Instead of:

```text
16dp
```

we define:

```text
Medium Spacing
```

Instead of:

```text
Roboto Bold 20sp
```

we define:

```text
Title Large
```

The meaning stays constant.

The implementation can change.

---

## Why Meaning Matters

Imagine the brand team decides to refresh the application's appearance.

The primary color changes.

Without a Design System,

developers search hundreds of files for:

```text
#0A84FF
```

With a Design System,

one token changes.

```text
Primary

↓

New Color
```

Every screen immediately follows the new design.

The application evolves consistently.

---

## A Shared Language

Imagine a conversation between a designer and a developer.

Instead of saying:

> **"Use the slightly darker blue with 18dp padding."**

They simply say:

> **"Use the Primary Button."**

Everyone understands exactly what that means.

A Design System creates a shared vocabulary for:

- designers,
- developers,
- QA,
- product managers.

That dramatically improves collaboration.

---

## An Analogy

Imagine a spoken language.

If every person invented their own grammar,

communication would quickly become impossible.

A Design System is the grammar of a product.

It defines the rules everyone follows,

allowing hundreds of people to build one coherent application.

---

## Thinking Like A Production Engineer

Whenever you're about to introduce:

- a new color,
- a new spacing,
- a new button,
- a new typography style,

don't ask:

> **Can I create this?**

Instead ask:

> **Does the Design System already solve this problem?**

If it does,

reuse it.

If it doesn't,

consider whether the Design System itself should evolve.

---

## Better Team Collaboration

Imagine the design team updates every Primary Button.

Without a Design System,

multiple engineers update multiple screens.

Some changes are forgotten.

The application becomes inconsistent.

With a Design System,

one component changes.

Every screen automatically reflects the new design.

Consistency becomes the default,

not something developers must remember.

---

## Connecting Back To The Atlas

Earlier,

we learned that application state should have a Single Source of Truth.

Now we've learned the same principle applies to design.

```text
Application State

↓

Single Source Of Truth
```

```text
Application Design

↓

Single Source Of Truth
```

Different domain.

Same architectural principle.

Great systems reduce independent decisions by centralizing shared knowledge.

---

## Beyond Compose

Every mature product eventually adopts a Design System.

You'll find them in:

- Material Design
- Fluent Design
- Apple Human Interface Guidelines
- Polaris
- Carbon
- Atlassian Design System

Different companies.

Different visual styles.

Same engineering principle.

Shared decisions create consistent products.

---

## Bringing Everything Together

```text
    Many Screens
        ↓
Many Design Decisions
        ↓
Independent Decisions?
        │
   ┌────┴────┐
   │         │
  Yes       No
   │         │
   ▼         ▼
Inconsistent  Design System
UI            Defines Decisions
                   │
                   ▼
          Consistent Product
```

Notice what the Design System centralizes.

Not components.

Decisions.

---

## Revision

### Core Idea

A Design System is the single source of truth for design decisions.

Instead of every screen inventing colors,

spacing,

typography,

and components independently,

the Design System defines them once for the entire product.

---

### Engineering Mindset

Don't think:

> **"A Design System is a component library."**

Think:

> **"A Design System is a shared language for building consistent products."**

Components are only one part of that language.

---

### Key Insight

The biggest benefit of a Design System isn't writing less UI code.

It's eliminating thousands of small design decisions,

allowing teams to build one coherent product instead of many independent screens.

---

## One Remaining Question

We've learned why applications need a Design System.

A natural question appears.

> **How does Jetpack Compose actually make colors, typography, spacing, and other design values available to every composable in the UI tree?**

That leads us to the next concept.

**Theming**
