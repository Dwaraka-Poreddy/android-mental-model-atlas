# Fragment

## Looking Back

In the previous chapters we learned:

```text
Android Process

↓

Application

↓

Activity

↓

One Screen
```

A natural question appears:

```text
Should

An Entire Screen

Always Be

One Large Unit?
```

---

## A Simple Observation

Imagine an e-commerce application.

The Product screen contains:

```text
Toolbar

↓

Product Details

↓

Reviews

↓

Recommended Products
```

These are different parts of the same screen.

A natural question appears:

```text
Can A Screen Be Built From Smaller, Reusable Pieces?
```

The answer is:

```text
Fragment
```

---

## Fragment

A Fragment represents:

```text
A Reusable Part Of A Screen
```

Conceptually:

```text
Application

↓

Activity

↓

Fragment
```

The Activity represents the entire screen.

A Fragment represents one section of that screen.

---

## Android Example

Consider a shopping application.

```text
Product Screen

↓

-------------------------

Toolbar

-------------------------

Product Details

-------------------------

Reviews

-------------------------

Recommended Products
```

Instead of implementing everything inside one large Activity,

the screen can be divided into multiple Fragments.

Conceptually:

```text
Product Activity

↓

Toolbar Fragment

↓

Product Details Fragment

↓

Reviews Fragment

↓

Recommendations Fragment
```

Each Fragment owns one part of the UI.

---

## Minimal Code

```kotlin
class ReviewsFragment : Fragment()
```

This declares a Fragment named `ReviewsFragment`.

---

## Production Code

```kotlin
class ReviewsFragment : Fragment() {

    override fun onViewCreated(
        view: View,
        savedInstanceState: Bundle?
    ) {

        loadReviews()

    }

}
```

### How To Read It

Don't worry about every API yet.

Simply recognize:

```text
ReviewsFragment

↓

Represents The Reviews Section Of The Screen

-------------------------

onViewCreated()

↓

This Part Of The UI Is Ready
```

The Fragment owns only its section of the screen.

---

## Production Notes

```text
• A Fragment represents part of a screen.

• Multiple Fragments can exist inside one Activity.

• Fragments help divide large screens into smaller, reusable pieces.

• Many existing Android applications use Fragments extensively.

• Many modern Compose applications use fewer Fragments or even a single Activity.
```

---

## Another Common Misconception

A common misconception is:

```text
Fragment = Another Activity
```

Incorrect.

```text
Activity

↓

Owns Entire Screen

-------------------------

Fragment

↓

Owns Part Of The Screen
```

A Fragment always exists as part of a screen.

---

## Putting Everything Together

```text
Android Process

↓

Application

↓

Activity

↓

Fragment

↓

Part Of Screen
```

Each level represents a smaller responsibility.

---

## A Natural Question

Now that we understand Fragments,

another question appears:

```text
If A Fragment Represents Part Of A Screen,

Who Creates It?

Who Removes It?

Who Replaces It?
```

Those questions lead us to the next chapter:

```text
FragmentManager
```

---

## Revision

### Core Idea

```text
Fragment

=

Reusable Part Of A Screen
```

### Mental Model

```text
Application

↓

Activity

↓

Fragment
```

### Production Recognition

```text
ReviewsFragment

↓

Owns The Reviews Section Of The Screen
```

### Previous Concept

```text
Activity
```

### Next Concept

```text
FragmentManager
```
