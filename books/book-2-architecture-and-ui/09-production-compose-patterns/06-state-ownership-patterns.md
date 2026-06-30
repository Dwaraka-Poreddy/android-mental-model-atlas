# State Ownership Patterns

## Observation

In the previous chapters,

we built a simple decision framework.

```text
New State

↓

Who Owns This Information?

↓

Choose The Owner

↓

Choose The State Container
```

State Hoisting wasn't the goal.

It was simply the consequence of choosing the correct owner.

A natural question appears.

> **What does this look like in real production applications?**

Let's explore the most common ownership patterns you'll encounter.

---

# Pattern 1 — Local UI State

Some state only matters to a single composable.

For example,

whether a dropdown menu is expanded.

```kotlin
@Composable
fun SortMenu() {

    var expanded by remember {
        mutableStateOf(false)
    }

    ...
}
```

Nobody else cares.

No other composable needs this information.

This is the perfect place for local state.

No hoisting required.

Other examples include:

- Password visibility
- Dialog visibility
- Dropdown expansion
- Animation progress
- Focus state

The composable owns the information,

so the composable owns the state.

---

# Pattern 2 — Shared Sibling State

Now imagine this hierarchy.

```text
Post

├── LikeButton
└── LikeCounter
```

Both composables need the same information.

```text
liked
```

If the button owns it,

the counter can't access it.

The natural solution is to move the state to their lowest common owner.

```text
Post

↓

liked

├── LikeButton
└── LikeCounter
```

Notice something.

We didn't move the state to the ViewModel.

We moved it only as high as necessary.

---

# Pattern 3 — Screen State

Suppose multiple parts of a screen depend on the same value.

```text
HomeScreen

├── Toolbar
├── Feed
├── Recommendations
└── BottomBar
```

All depend on:

```text
selectedCategory
```

Now the screen itself becomes the natural owner.

```text
HomeScreen

↓

selectedCategory

├── Toolbar
├── Feed
├── Recommendations
└── BottomBar
```

Again,

the ownership follows responsibility.

---

# Pattern 4 — Business State

Now consider this information.

```text
Logged-in User
```

Who owns it?

Certainly not:

- Toolbar
- Feed
- HomeScreen

This is business information.

It may survive:

- recomposition,
- configuration changes,
- navigation,
- process recreation.

The natural owner is the ViewModel (or another state holder).

The UI simply observes it.

---

# Pattern 5 — Temporary Editing State

This pattern surprises many developers.

Imagine editing a user profile.

The ViewModel contains:

```text
John Smith
```

The user begins typing.

Should every keystroke immediately update the ViewModel?

Not always.

Often,

it's better to create a temporary editing copy.

```text
ViewModel

↓

Original Profile

↓

remember()

↓

Editing Copy

↓

User Presses Save

↓

Update ViewModel
```

This allows the user to:

- edit freely,
- cancel changes,
- validate input,
- save only when ready.

The ViewModel still owns the original data.

The composable temporarily owns the editing state.

Both ownership decisions are correct.

---

# The Lowest Common Owner Principle

All of these patterns follow one simple rule.

```text
Who Needs This Information?

↓

Find The Lowest
Common Owner

↓

Store It There
```

This single principle explains most state ownership decisions in Compose.

---

# When NOT To Hoist State

After learning State Hoisting,

many developers start moving every piece of state upward.

That's another overcorrection.

State should remain local when it only controls local UI behavior.

Good examples include:

- Dropdown expansion
- Password visibility
- Focus state
- Ripple state
- Animation progress
- Scroll position (unless another owner genuinely needs it)

These states don't represent business information.

Keeping them local usually results in simpler code.

Remember.

The goal isn't to hoist state.

The goal is correct ownership.

---

# Thinking Like A Production Engineer

Whenever you introduce new state,

mentally walk this decision tree.

```text
                    New State
                         │
                         ▼
          Who Owns This Information?
                         │
        ┌────────────────┴────────────────┐
        │                                 │
        ▼                                 ▼
 Only This Composable?            Multiple Composables?
        │                                 │
        ▼                                 ▼
   Keep It Local              Lowest Common Owner
                                          │
                                          ▼
                              Business Information?
                                   │          │
                                   ▼          ▼
                              Screen     ViewModel
```

This simple framework solves the vast majority of ownership questions you'll encounter.

---

# Bringing Everything Together

The previous four chapters weren't teaching separate ideas.

They were answering one question from different perspectives.

```text
Who Owns This Information?

↓

Choose The Owner

↓

Stateful

or

Stateless

↓

Hoist If Necessary

↓

Apply The Appropriate Pattern
```

Once ownership is clear,

the architecture becomes much easier to design.

---

## Revision

### Core Idea

Correct ownership determines:

- where state lives,
- who updates it,
- who reads it,
- and whether it should be hoisted.

### Engineering Mindset

Don't memorize rules like:

- "Always hoist state."
- "Always use ViewModel."

Instead ask:

> **Who is responsible for this information?**

Everything else naturally follows.

### Key Insight

Every state ownership decision in Compose is simply an application of one principle:

> **Store state in the lowest owner that is genuinely responsible for it.**

---

## One Remaining Question

So far,

we've focused on state and UI.

A natural question appears.

> **What happens when a composable needs to perform work outside of simply describing the UI?**

For example:

- launching a coroutine,
- making a network request,
- showing a snackbar,
- registering a listener.

That leads us into the next part of the document.

**Why Side Effects Exist**
