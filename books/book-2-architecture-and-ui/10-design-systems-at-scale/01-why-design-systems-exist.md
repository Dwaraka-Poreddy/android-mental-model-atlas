# Why Design Systems Exist

## Looking Back

In DOC 9 we learned about `MaterialTheme`

and how to define colors, typography, and shapes in one place.

We learned that CompositionLocal carries those values

implicitly down the Composition Tree,

so every composable can read `MaterialTheme.colorScheme.primary`

without having it passed as a parameter.

That was one team, one codebase.

Now imagine ten teams.

---

## A Story About Six Months

Imagine a food delivery app.

Ten developers. Three feature teams.

Month one:

- Team A builds the Login screen.
- Team B builds the Home screen.
- Team C builds the Profile screen.

Everyone is moving fast.

Everyone makes small local decisions.

---

Month six.

A designer opens the app on their phone.

They notice something wrong.

```text
Login screen     → primary blue is #1565C0
Home screen      → primary blue is #1976D2
Profile screen   → primary blue is #0D47A1
```

Three different blues.

All close enough to look like the same color.

None of them the same color.

---

The designer files a bug.

> "Make all the blues consistent."

The developer opens a search.

```text
Search: "0xFF1565C0"   →  14 files
Search: "0xFF1976D2"   →   9 files
Search: "0xFF0D47A1"   →  17 files
```

Forty files.

Some are in screens.

Some are in components.

Some are inside lambdas.

One developer. Three days. Forty files.

---

## The Deeper Problem

The color fix is the symptom.

The real problem is structural.

```text
Without a Design System

Designer says: "Change the primary color"
        ↓
Developer hunts through 40 files
        ↓
Misses 3 usages
        ↓
App looks inconsistent
        ↓
User trust degrades
```

And it's not just colors.

---

Six months in, the codebase also contains:

```text
feature/auth/AuthCard.kt
feature/home/MenuItemCard.kt
feature/profile/ProfileInfoCard.kt
```

Three different card components.

Each one slightly different.

None of them imported by the others.

None of them reusable across teams.

Design update: "All cards should have 8dp corner radius and 1dp border."

Update three files.

Miss one.

Inconsistency again.

---

## What This Costs

This isn't just inconvenient.

It creates a permanent gap between design and engineering.

```text
Designer intention        Developer implementation
        ↓                          ↓
One primary color          Three similar colors
One card component         Three similar cards
One button style           Four similar buttons
```

The wider this gap,

the more trust erodes between teams.

New designers join.

They produce Figma specs.

Someone has to manually cross-reference those specs

against forty scattered files

every time something changes.

That becomes a full-time job.

---

## The Mental Model

A design system is a shared language

between designers and developers.

```text
Design System = Single Source of Truth
                for how the app looks and feels
```

Not a folder.

Not a style guide document.

A living system of named values and reusable components

that both designers and developers reference.

---

## What It Looks Like

```text
Design System
        │
        ├── Design Tokens
        │   (colors, spacing, type scales)
        │           ↓
        ├── Component Library
        │   (buttons, cards, inputs)
        │           ↓
        └── Patterns
            (how components combine)
                    ↓
        Feature Teams use all of the above
        (they never define colors directly)
```

Notice the direction.

Feature teams are consumers.

The design system is the producer.

Feature teams don't invent colors.

They ask the design system for them.

---

## What a Design System Enforces

```text
No hardcoded colors in feature code
        ↓
Colors always come from tokens

No local Button definitions in features
        ↓
All buttons come from the component library

No "almost the same" card components
        ↓
One Card component, configured through parameters
```

When the designer says "change the primary color,"

one file changes.

Every screen updates.

Zero missed usages.

---

## When Does This Start Mattering?

Not everyone needs this on day one.

```text
1 developer, 5 screens
        ↓
Hardcoded colors are fine

3 developers, 20 screens
        ↓
Starting to feel the pain

5+ developers, 50+ screens
        ↓
Design system is no longer optional
```

The inflection point for most teams:

three or more feature teams working in parallel,

or fifty or more screens,

whichever comes first.

---

## Production Notes

- Design systems are infrastructure —
  they slow you down initially
  and speed you up at scale.

- The inflection point:
  most teams feel the need around
  three to four feature teams
  or fifty-plus screens.

- A design system is a contract between
  designers and developers.
  Both sides must buy in.
  A system only designers use
  or only developers use
  doesn't work.

- Without tooling support
  (Figma tokens, Compose previews, component documentation),
  design systems drift over time.
  Someone always adds one hardcoded color
  "just for now."

- Large companies often have dedicated
  "design system teams"
  whose only job is maintaining this infrastructure.

---

## Common Misconception

> **"We're too small to need a design system."**

Even two-person teams benefit from token-based colors

and a shared component file.

The cost is low.

The consistency gain is immediate.

You don't need a formal process.

You need one file that everyone reads colors from,

and one file that everyone imports buttons from.

That's already a design system.

---

## A Natural Question

We keep saying "design tokens."

What exactly is a token?

How does naming a value give us all these benefits?

> **Next: Design Tokens**

---

## Revision

### Core Idea

```text
Without a Design System
        ↓
Inconsistency grows with team size
        ↓
Design-to-code gap becomes expensive

With a Design System
        ↓
Single source of truth for values and components
        ↓
One change propagates everywhere
```

### Mental Model

```text
Design System = Shared Language

Designers define it.
Developers implement it.
Feature teams consume it.
No one works around it.
```

### Engineering Mindset

Don't think:

> **"What color should this button be?"**

Think:

> **"What token represents the primary action color,
>    and where is it defined?"**

The design system answers both questions in one place.

### Key Insight

Inconsistency in large apps is never a designer problem

or a developer problem.

It's a coordination problem.

Design systems solve coordination.
