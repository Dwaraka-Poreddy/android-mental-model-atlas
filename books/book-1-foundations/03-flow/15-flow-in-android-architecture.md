# Flow In Android Architecture

## A Question From The Previous Chapter

Over the last several chapters we have learned how to:

```text
Transform Values

Filter Values

Reduce Noise

Combine Flows

Produce New Flows

Execute Pipelines
```

A natural question appears:

```text
How Do All Of These Pieces Work Together Inside A Real Android Application?
```

Rather than studying each operator in isolation, this chapter follows one complete journey of data through an Android application.

---

## The Big Picture

A typical Android application is organized into layers.

Conceptually:

```text
Repository

↓

ViewModel

↓

UI
```

Each layer has a different responsibility.

---

The repository knows:

```text
How To Obtain Data
```

---

The ViewModel knows:

```text
How To Prepare Data For The UI
```

---

The UI knows:

```text
How To Display Data
```

---

Flow naturally connects these layers together.

---

## A Simple Example

Suppose we have a user profile screen.

The repository exposes:

```kotlin
fun observeUser(
    userId: String
): Flow<User>
```

Conceptually:

```text
Repository

↓

Flow<User>
```

---

Nothing here knows anything about:

```text
Buttons

Text

Images

Compose

RecyclerView
```

Its only responsibility is:

```text
Provide Data
```

---

## The ViewModel

The ViewModel receives:

```text
Flow<User>
```

and transforms it into:

```text
Flow<UserUiModel>
```

For example:

```kotlin
val userUiFlow =
    repository
        .observeUser(userId)
        .map { user ->

            UserUiModel(
                name = user.name.uppercase(),
                isPremium = user.isPremium
            )
        }
```

Conceptually:

```text
Repository

↓

Flow<User>

↓

map()

↓

Flow<UserUiModel>
```

The ViewModel is preparing data for presentation.

---

## The UI

The UI executes the pipeline.

Conceptually:

```text
Flow<UserUiModel>

↓

collect()

↓

Render Screen
```

Example:

```kotlin
viewModel.userUiFlow.collect { uiModel ->

    render(uiModel)
}
```

---

The complete journey becomes:

```text
Repository

↓

Flow<User>

↓

map()

↓

Flow<UserUiModel>

↓

collect()

↓

UI
```

---

## Search Screen

Search demonstrates several operators working together.

The user types:

```text
a

an

and

andr

andro

android
```

Every keystroke produces a value.

---

The pipeline might look like:

```kotlin
searchQueryFlow
    .debounce(300)
    .flatMapLatest { query ->

        repository.search(query)
    }
    .map { results ->

        results.sortedBy { it.name }
    }
```

Conceptually:

```text
Search Query

↓

debounce()

↓

flatMapLatest()

↓

map()

↓

Search Results
```

---

Each operator has one responsibility.

---

```text
debounce()

↓

Ignore Rapid Typing
```

---

```text
flatMapLatest()

↓

Keep Only The Latest Search
```

---

```text
map()

↓

Prepare Results For Display
```

---

Instead of solving one huge problem:

```text
Search
```

the pipeline solves many small problems.

---

## Dashboard Screen

Many screens depend on multiple data sources.

For example:

```text
User

Settings

Notifications
```

Each may come from its own Flow.

The ViewModel combines them.

```kotlin
val dashboardFlow =
    combine(
        userFlow,
        settingsFlow,
        notificationsFlow
    ) { user, settings, notifications ->

        DashboardUiModel(
            user,
            settings,
            notifications
        )
    }
```

Conceptually:

```text
User Flow

+

Settings Flow

+

Notifications Flow

↓

combine()

↓

DashboardUiModel
```

---

The UI collects only one Flow:

```text
DashboardUiModel
```

even though many sources contribute to it.

---

## Chat Screen

Suppose the user selects:

```text
General
```

Conceptually:

```text
Chat Id

↓

observeMessages()

↓

Flow<List<Message>>
```

The user now switches to:

```text
Support
```

A new Flow becomes relevant.

The ViewModel naturally uses:

```text
flatMapLatest()
```

to observe only:

```text
The Currently Selected Chat
```

---

Again, each operator solves one problem.

---

## Building Pipelines

Notice that every layer adds another step.

Repository:

```text
Provide Data
```

↓

ViewModel:

```text
Transform Data
```

↓

ViewModel:

```text
Combine Data
```

↓

ViewModel:

```text
Keep Latest Data
```

↓

UI:

```text
Collect Data
```

---

Instead of one large operation:

```text
Many Small Composable Steps
```

build one pipeline.

---

## Another Mental Model

Imagine a factory.

Raw materials arrive.

```text
Raw Material

↓

Cut

↓

Paint

↓

Assemble

↓

Package
```

Every stage has one responsibility.

No stage needs to understand the entire factory.

---

Flow pipelines work in the same way.

```text
Raw Data

↓

map()

↓

filter()

↓

combine()

↓

collect()

↓

UI
```

Each operator performs one small transformation.

---

## Another Common Misconception

A common misconception is:

```text
Repositories Should Return UI Models
```

Usually:

```text
No
```

Repositories expose:

```text
Domain Data
```

The ViewModel transforms that data into:

```text
UI Data
```

using Flow operators.

---

## Another Common Misconception

A common misconception is:

```text
One Huge Flow Should Solve Every Problem
```

Instead, most applications compose many simple operators.

Conceptually:

```text
Transform

↓

Filter

↓

Combine

↓

Execute
```

Small responsibilities produce pipelines that are easier to understand and maintain.

---

## Putting Everything Together

This single diagram summarizes the Flow journey.

```text
Repository

↓

Flow<User>

↓

map()

↓

combine()

↓

flatMapLatest()

↓

Flow<UserUiModel>

↓

collect()

↓

UI
```

Every chapter in this document contributed one piece of this picture.

---

## The Key Mental Model

Flow is not just:

```text
A Way To Return Data
```

It is a way to describe:

```text
How Data Moves Transforms Combines And Eventually Reaches The UI
```

through a sequence of small, composable operations.

---

## Revision Table

| Section | Content |
|----------|----------|
| Architecture Layers | Repository → ViewModel → UI |
| Repository Responsibility | Provide Data |
| ViewModel Responsibility | Transform & Combine Data |
| UI Responsibility | Execute & Render |
| Common Operators | map, debounce, combine, flatMapLatest |
| Pipeline Philosophy | Many Small Transformations |
| Mental Model | Factory Assembly Line |
| Previous Concept | Executing Flow Pipelines |
| Next Document | Flow Lifecycle & Sharing |

---

## Looking Back

We began this document with one question:

```text
Why Does Flow Exist?
```

Along the way we learned:

```text
What A Flow Is

↓

How Values Move

↓

How Values Transform

↓

How Multiple Flows Work Together

↓

How Values Produce Flows

↓

How Latest Flows Replace Old Ones

↓

How Pipelines Execute
```

Now those ideas come together into one complete picture:

```text
Repository

↓

Flow

↓

Operators

↓

ViewModel

↓

collect()

↓

UI
```

---

## Looking Ahead

Throughout this document we explored how data flows through an Android application.

We learned how to:

```text
Produce Data

↓

Transform Data

↓

Filter Data

↓

Combine Data

↓

Execute Pipelines
```

> **You'll see this in...**
> - **DOC 5 — Android Lifecycle & Architecture**, where Repositories expose `Flow` to ViewModels, forming the backbone of unidirectional data flow
> - **DOC 12 — Local Persistence**, where Room returns `Flow<List<Entity>>` so your UI automatically updates when the database changes
> - **DOC 13 — Data Architecture**, where offline-first architectures combine Room Flows and Retrofit calls into a single data pipeline
> - **DOC 9 — Production Compose Patterns**, where `collectAsState()` bridges Flow into Compose's snapshot system

However, moving data is only one part of building reactive applications.

Equally important is deciding:

```text
How Different Kinds Of Information Should Be Represented
```

In the next document, **State Management**, we will explore concepts such as:

```text
State

StateFlow

SharedFlow

Channel

State vs Event
```

and build mental models for representing state, shared data, and one-time events in Android applications.
