# Folder Structure

## Observation

Imagine two Android projects.

The first project looks like this.

```text
app/

    MainActivity.kt

    HomeScreen.kt

    LoginScreen.kt

    ProfileScreen.kt

    UserRepository.kt

    Button.kt

    Theme.kt

    Utils.kt

    ...
```

Everything lives together.

The application works perfectly.

Now imagine another project.

```text
feature/

    home/

    profile/

    checkout/

core/

    ui/

    designsystem/

    network/

    database/
```

This application also works perfectly.

A natural question appears.

> **Which folder structure is correct?**

---

## The Wrong Question

Most developers ask:

> **"What's the best folder structure?"**

That's actually the wrong question.

Because the answer changes.

Different companies organize projects differently.

Some organize by:

- layers,
- features,
- domains,
- modules.

All of these approaches can be successful.

The folder structure itself isn't what makes an application maintainable.

---

## The Real Question

Instead ask:

> **Why do we organize projects at all?**

Folders don't exist for Android Studio.

They exist for developers.

Their purpose is to help people understand,

navigate,

and maintain large codebases.

---

## Imagine The Project Keeps Growing

Today,

your application contains:

```text
10 Files
```

Finding anything is easy.

A few months later,

the project contains:

```text
100 Files
```

Still manageable.

A few years later,

it contains:

```text
2,000 Files
```

Suddenly,

finding the right file becomes much harder.

Good organization reduces the amount of searching developers need to do.

---

## This Should Feel Familiar

Earlier,

we learned to decompose large composables into smaller composables.

Instead of writing one giant UI,

we split it into meaningful pieces.

Now we're doing exactly the same thing.

Not for UI.

But for the project itself.

Instead of one giant folder,

we organize related code into meaningful groups.

The same engineering principle appears again.

```text
Break Large Things

↓

Into Smaller,

Related Pieces
```

---

## Group By Responsibility

Imagine everything related to the Home feature lives together.

```text
home/

    HomeScreen.kt

    HomeViewModel.kt

    HomeRepository.kt

    HomeUiState.kt
```

Notice something.

These files are closely related.

They usually change together.

Keeping them together makes the feature easier to understand.

Now imagine another file.

```text
Button.kt
```

This button is used across the entire application.

It doesn't belong to Home.

It belongs somewhere shared.

Different responsibilities naturally lead to different locations.

---

## Cohesion

Good folder structures have one important characteristic.

Related things stay together.

Engineers often call this **cohesion**.

Imagine changing the Home feature.

Which files are you likely to edit?

Probably:

- `HomeScreen`
- `HomeViewModel`
- `HomeRepository`
- `HomeUiState`

Because they work together,

placing them together reduces the effort required to understand the feature.

---

## There Is No Universal Folder Structure

A common misconception is that one folder structure is always superior.

For example,

some projects organize by layers.

```text
screens/

viewmodels/

repositories/
```

Others organize by features.

```text
home/

checkout/

profile/
```

Neither approach is universally correct.

Each optimizes for different teams,

different products,

and different workflows.

The important question isn't:

> **Which structure is fashionable?**

Instead ask:

> **Can another engineer quickly find the code they're looking for?**

That's the real measure of a good organization.

---

## Organize Around How People Think

Imagine a new engineer joins the team.

They receive a task.

> **"Add a discount badge to the Checkout screen."**

What's the first thing they'll look for?

Probably:

```text
checkout/
```

Not:

```text
viewmodels/

screens/

repositories/
```

Feature-oriented structures often match how product teams naturally think about the application.

Again,

this isn't a rule.

It's simply one possible optimization.

---

## Connecting Everything We've Learned

Notice how every concept in this document connects together.

We learned to decompose:

- composables,
- state,
- responsibilities,
- design systems,
- themes.

Now we're decomposing the project itself.

The principle has never changed.

```text
Large System

↓

Smaller Responsibilities

↓

Easier To Understand

↓

Easier To Maintain
```

Good architecture is simply the repeated application of this idea at different scales.

---

## Thinking Like A Production Engineer

Experienced engineers rarely ask:

> **"Where should this file go?"**

Instead,

they ask:

> **"What responsibility does this code belong to?"**

Once the responsibility is clear,

its location usually becomes obvious.

Folders should reflect responsibilities,

not convenience.

---

## Looking Ahead

Eventually,

even folders become too large.

A single application might contain:

- hundreds of features,
- multiple teams,
- millions of lines of code.

At that point,

engineers often introduce another level of organization:

```text
Modules
```

Instead of simply organizing files,

they organize entire features into independent building blocks.

We'll explore that in future Atlas documents.

---

## Bringing Everything Together

```text
Growing Project

↓

More Files

↓

Need Better Organization

↓

Group By Responsibility

↓

Higher Cohesion

↓

Easier Navigation

↓

Easier Maintenance
```

Notice something.

Folders don't improve architecture.

They improve the ability of humans to understand architecture.

---

## Revision

### Core Idea

Folder structures exist to help developers understand,

navigate,

and evolve large codebases.

There is no universally correct structure.

The best organization is the one that makes related code easy to find and maintain.

---

### Mental Model

```text
Large Project

↓

Break Into

Related Responsibilities

↓

Organize Together

↓

Easier To Understand
```

---

### Engineering Mindset

Don't think:

> **"Where should this file go?"**

Think:

> **"What responsibility does this code belong to?"**

Good folder structures naturally emerge from good responsibility boundaries.

---

### Key Insight

Folder structures are not architectural patterns.

They're simply a way of expressing the architecture you've already designed.

If the architecture is unclear,

no folder structure can fix it.

If the architecture is clear,

many different folder structures can work well.

---

## Looking Back

Throughout this document,

we gradually expanded our perspective.

We started by understanding how individual composables behave.

Then we learned how to:

- manage state,
- perform side effects,
- optimize recomposition,
- design reusable UI,
- build design systems,
- distribute themes,
- organize applications.

Notice the progression.

```text
One Composable

↓

One Screen

↓

One Feature

↓

One Application
```

Each chapter zoomed out a little further,

building the mental model required to design real production applications.

---

## One Remaining Question

We've now learned how Compose applications are structured.

A natural question appears.

> **Where does the actual data displayed by these applications come from, and how does it travel from the network all the way to the UI?**

That begins the next document.

**Production Android Architecture**
