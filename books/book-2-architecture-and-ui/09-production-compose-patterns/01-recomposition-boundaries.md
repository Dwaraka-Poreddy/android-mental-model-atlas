# Recomposition Boundaries

## Observation

In Doc 7,

we learned how the Compose Runtime processes a state change.

```text
State Changed
      │
      ▼
Track State Reads
      │
      ▼
Find Restart Groups
      │
      ▼
Skip Unaffected Groups
      │
      ▼
Update LayoutNode Tree
      │
      ▼
   Measure
      │
      ▼
    Layout
      │
      ▼
    Draw
```

Looking at this pipeline,

a natural question appears.

> **If Compose performs all of these steps every time state changes, why does it still feel so fast?**

To answer that,

we first need to understand **what actually recomposes**.

---

## A Production Example

Consider this screen.

```kotlin
@Composable
fun HomeScreen(
    uiState: HomeUiState
) {
    Header()

    Feed(
        posts = uiState.posts
    )

    BottomBar()
}
```

Now suppose a new post arrives.

```kotlin
uiState.posts = updatedPosts
```

Pause for a moment and predict the result.

**Which composables do you expect Compose to execute again?**

Before reading further,

make a prediction.

Learning to predict recomposition is one of the most valuable Compose skills you can develop.

---

## How Experienced Engineers Read This Code

A beginner often reads this as one large screen.

```text
HomeScreen
```

An experienced Compose developer sees something different.

```text
HomeScreen
    ├── Header
    ├── Feed
    └── BottomBar
```

They immediately begin asking questions.

- Which state does `Header()` read?
- Which state does `Feed()` read?
- Which state does `BottomBar()` read?

Those questions matter because **state reads determine recomposition**.

---

## Runtime Reasoning

From Doc 7,

we already know that Compose tracks every state read.

In this example,

only `Feed()` depends on `uiState.posts`.

Conceptually,

Compose reasons like this.

```text
  posts changed
        │
        ▼
Feed depends on posts?
        │
       Yes
        │
        ▼
  Recompose Feed
```

For the other composables,

the answer is different.

```text
  posts changed
        │
        ▼
Header depends on posts?
        │
       No
        │
        ▼
      Skip
```

```text
posts changed
        │
        ▼
BottomBar depends on posts?
        │
       No
        │
        ▼
      Skip
```

Notice what Compose **didn't** do.

It didn't blindly execute every composable on the screen.

It identified the affected recomposition boundary and skipped the rest.

This is the practical benefit of everything we learned in Doc 7.

---

## A Common Production Mistake

Now consider a slightly different implementation.

```kotlin
@Composable
fun HomeScreen(
    uiState: HomeUiState
) {
    val unreadCount = uiState.notifications.count()

    Header(
        unreadCount = unreadCount
    )

    Feed(
        posts = uiState.posts
    )

    BottomBar()
}
```

Suppose another post arrives.

The notifications haven't changed.

The unread count hasn't changed.

Yet something interesting happens.

`HomeScreen()` executes again.

That means this line executes again too.

```kotlin
val unreadCount = uiState.notifications.count()
```

The result may be identical,

but the computation still happened.

In this example,

the cost is tiny.

But imagine replacing it with:

```kotlin
val recommendations =
    recommendationEngine.generate(uiState)
```

or

```kotlin
val groupedMessages =
    messages.groupBy { it.senderId }
```

Now you're performing unnecessary work during every recomposition.

Compose successfully skipped parts of the UI,

but your own code still performed work that wasn't needed.

---

## Thinking Like A Production Engineer

An experienced Compose developer immediately notices this.

Not because recomposition is bad.

Because **work is happening in the wrong place.**

Instead of asking:

> "Can I stop recomposition?"

they ask:

> **"Can I reduce the work performed during recomposition?"**

That's a much more useful question.

---

## A Better Design

One option is to move unrelated work outside this recomposition boundary.

```kotlin
@Composable
fun HomeScreen(
    posts: List<Post>,
    unreadCount: Int
) {
    Header(
        unreadCount = unreadCount
    )

    Feed(
        posts = posts
    )

    BottomBar()
}
```

Now,

changes to the feed no longer require recalculating the unread count inside this composable.

More importantly,

each composable receives only the data it actually needs.

Smaller,

focused composables naturally create better recomposition boundaries.

---

## Why This Matters

Compose performance isn't just determined by the runtime.

It's also determined by **how you structure your UI.**

Good composable decomposition gives Compose the opportunity to skip work.

Poor decomposition forces unrelated work to happen together.

That's why experienced Compose developers spend time thinking about composable boundaries,

not just composable size.

---

## Production Recognition

When reading Compose code,

don't immediately ask:

> "How many composables are on this screen?"

Instead ask:

- Where are the recomposition boundaries?
- Which state does each boundary observe?
- What work happens inside each boundary?
- Could unrelated work be separated?

Those questions lead to better Compose code.

---

## Revision

### Core Idea

```text
State Changed
      │
      ▼
Find The Affected Recomposition Boundary
      │
      ▼
Skip Everything Else
```

### Engineering Mindset

Don't think in terms of **screens**.

Think in terms of **recomposition boundaries**.

### The Key Insight

Compose minimizes **runtime work**.

Your job is to minimize **application work** inside each recomposition boundary.

Those two optimizations work together.

---

## One Remaining Question

We've learned that Compose only recomposes the necessary boundaries.

A natural question appears.

> **If recomposition is already so selective, what actually makes a Compose screen slow?**

That leads us to the next concept.

**Recomposition Cost**
