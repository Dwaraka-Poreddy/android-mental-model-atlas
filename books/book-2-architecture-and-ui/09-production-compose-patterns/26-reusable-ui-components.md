# Reusable UI Components

## Observation

Imagine we're building two different screens.

The Home screen contains:

```text
Profile Card

↓

Avatar

↓

Name

↓

Email
```

The Settings screen contains:

```text
Profile Card

↓

Avatar

↓

Name

↓

Email
```

The UI looks almost identical.

A natural question appears.

> **Should we simply copy the code?**

---

## The First Thought

Many developers do exactly that.

```text
HomeScreen.kt

↓

Profile Card
```

Copy.

Paste.

```text
SettingsScreen.kt

↓

Profile Card
```

Everything works.

Until the design changes.

Now the Profile Card needs to display:

- online status,
- a verification badge,
- a new spacing system.

Suddenly,

the same change has to be implemented in multiple places.

As the application grows,

those copies slowly drift apart.

The UI becomes inconsistent.

---

## The Obvious Solution

Instead of copying,

we extract the shared concept.

```kotlin
@Composable
fun ProfileCard(

    user: User

) {

    ...

}
```

Now every screen simply writes:

```kotlin
ProfileCard(user)
```

One implementation.

Multiple usages.

Future changes happen in one place.

---

## But Then Another Question Appears

Should we extract **every** repeated piece of UI?

No.

Absolutely not.

Similarity alone isn't enough.

---

## The Danger Of Premature Reuse

Imagine two screens both contain:

```kotlin
Text("Welcome")
```

Should we immediately create:

```kotlin
WelcomeText()
```

Probably not.

Today,

both happen to display the same text.

Tomorrow,

one screen becomes:

```text
Welcome Back
```

The other becomes:

```text
Hello, John
```

The similarity disappeared.

The abstraction no longer represents reality.

We created it too early.

---

## The Bigger Principle

Don't extract code because it **looks similar**.

Extract it because it represents the **same responsibility**.

That's a much stronger reason.

Visual similarity is temporary.

Shared meaning lasts much longer.

---

## A Better Example

Imagine these screens.

Checkout.

```text
Price Card
```

Order History.

```text
Price Card
```

Product Details.

```text
Price Card
```

These aren't merely similar.

They're the same business concept.

That's an excellent candidate for reuse.

Now compare that with:

```text
Blue Rounded Card
```

appearing on two screens.

Those components only share an appearance.

Their responsibilities may be completely different.

Appearance changes frequently.

Meaning usually doesn't.

---

## Reuse Knowledge, Not Just Code

This is one of the most important ideas in this chapter.

Many developers think reuse is about avoiding duplicate code.

A more useful way to think about it is:

> **Avoid duplicating business knowledge.**

Suppose the business decides:

> Every Profile Card must now display the user's online status.

How many places should you modify?

Ideally,

one.

The business rule exists once.

The implementation should too.

Good reusable components centralize business knowledge,

not just UI code.

---

## The Danger Of Over-Generalization

Sometimes developers become too enthusiastic about reuse.

Imagine this component.

```kotlin
UniversalCard(

    title,

    subtitle,

    leftIcon,

    rightIcon,

    padding,

    spacing,

    shape,

    color,

    elevation,

    ...

)
```

Eventually,

the component accepts dozens of parameters.

Technically,

it's reusable.

Practically,

nobody enjoys using it.

The abstraction became too generic.

Instead of simplifying the codebase,

it increased complexity.

---

## A Better Rule

A reusable composable should represent something people naturally recognize.

Good examples include:

- Profile Card
- Product Card
- Transaction Row
- Balance Card
- Search Bar

These names describe **purpose**.

Now compare them with:

- RoundedBlueContainer
- GrayCard
- LeftAlignedBox

These names describe **appearance**.

Appearance changes.

Purpose usually survives redesigns.

Build components around meaning,

not styling.

---

## Thinking Like A Production Engineer

Whenever you're tempted to extract a reusable component,

don't ask:

> **Can I reuse this code?**

Instead ask:

> **Does this represent the same concept everywhere it's used?**

If the answer is yes,

it's probably a good reusable component.

If the answer is no,

duplicating a small amount of code may actually produce a healthier architecture.

---

## Better Team Collaboration

Imagine the design team updates the Balance Card.

Without reuse,

multiple engineers modify multiple screens.

Some updates are forgotten.

The application becomes inconsistent.

With a shared component,

one implementation changes.

Every screen benefits immediately.

Reuse isn't just about writing less code.

It's about keeping the product consistent.

---

## Beyond Compose

The same principle appears throughout software engineering.

- Shared utility functions
- Common business services
- Shared API clients
- Database repositories
- Design systems

Different technologies.

Same idea.

Don't duplicate concepts.

Represent them once.

---

## Bringing Everything Together

```text
      Repeated UI
          ↓
      Is It The Same
      Business Concept?
            │
     ┌──────┴──────┐
     │             │
    Yes           No
     │             │
     ▼             ▼
Extract      Keep Separate
Reusable     Components
Component
     │
     ▼
One Concept
One Implementation
```

Notice what we're optimizing.

Not line count.

Not file size.

We're optimizing the number of places where a business concept is implemented.

---

## Revision

### Core Idea

Reusable UI components should represent shared business concepts,

not merely similar pieces of code.

The goal is to centralize knowledge,

making the application easier to evolve and keep consistent.

---

### Engineering Mindset

Don't think:

> **"Can I reuse this code?"**

Think:

> **"Am I representing one reusable concept?"**

Meaning drives good abstractions.

Similarity alone doesn't.

---

### Key Insight

The best reusable components are organized around **purpose** rather than **appearance**.

Purpose survives redesigns.

Appearance rarely does.

---

## One Remaining Question

We've learned how to build reusable UI components.

A natural question appears.

> **As an application grows to hundreds of reusable components, colors, typography styles, spacing rules, and icons, how do teams keep everything visually consistent?**

That leads us to the next concept.

**Design System**
