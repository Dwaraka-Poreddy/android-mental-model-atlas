# Why Dependency Injection Exists

## Looking Back

In DOC 11 we built a `UserRemoteDataSource`
that wraps Retrofit and fetches user data from the network.

In DOC 12 we built a `UserDao`
that reads and writes user data to a local Room database.

In DOC 13 we built a `UserRepository`
that coordinates between the two:

```text
UserRepository

↓

Coordinator Between Network And Database
```

These three pieces form a complete data pipeline.

The pipeline is built.

A natural question appears:

```text
How Does A ViewModel Get A UserRepository?
```

---

## The Problem

To use a `UserViewModel` in an Activity,
you have to build it.

Building it means building everything it needs.

Building everything it needs means building everything
those things need.

```kotlin
// In every Activity that needs UserViewModel:
val db = Room.databaseBuilder(
    context, AppDatabase::class.java, "app.db"
).build()
val dao = db.userDao()
val api = Retrofit.Builder()
    .baseUrl("https://api.example.com/")
    .build()
    .create(UserApi::class.java)
val remote = UserRemoteDataSource(api)
val repo = UserRepository(remote, dao)
val viewModel = UserViewModel(repo)
```

Seven lines of setup before you reach the ViewModel.

And this is only one screen.

---

## Three Things Wrong With This

**Problem 1: Every screen repeats this construction code.**

`HomeActivity`, `ProfileActivity`, `OrdersActivity` —

every screen that needs user data
builds the entire stack from scratch.

Construction knowledge for the data layer
now lives in every UI file.

**Problem 2: Adding a dependency updates every screen.**

`UserRepository` needs a new `AnalyticsTracker`.

You update `UserRepository`'s constructor.

Now every screen that builds a `UserRepository` must change.

Ten screens: ten file edits.

Twenty screens: twenty file edits.

One logical change.

Many mechanical updates.

**Problem 3: Testing requires replacing objects — impossible here.**

To unit-test `UserViewModel`,
you want to replace `UserRemoteDataSource` with a fake
that returns predictable test data without a network call.

But the real `UserRemoteDataSource` is created inside the construction cascade.

There is no way to swap it.

```text
Object creates its own dependencies.

↓

No seam to insert a fake.

↓

Unit testing requires the real network.
```

---

## The Core Insight

All three problems share a single cause:

```text
Objects create their own dependencies.
```

The fix:

```text
Objects receive their dependencies from the outside.
```

This is the mental model for this entire document:

```text
Dependency Injection

↓

Objects Receive Their Dependencies From The Outside,
Rather Than Creating Them
```

The word "injection" simply means "passed in."

A dependency is injected when
it is given to an object
rather than built by the object.

---

## The Before And After

```kotlin
// WITHOUT DI — ViewModel builds its own dependencies
class UserViewModel : ViewModel() {
    private val db = Room.databaseBuilder(...)
    private val repo = UserRepository(
        UserRemoteDataSource(Retrofit.Builder()...),
        db.userDao()
    )
}

// WITH DI — ViewModel receives its dependencies
class UserViewModel(
    private val userRepository: UserRepository
) : ViewModel()
```

The second ViewModel does one thing:

it declares what it needs.

It does not know how `UserRepository` is built.

It does not know what `UserRemoteDataSource` is.

It does not know Room exists.

---

## The Diagram

```text
Without DI                           With DI

Activity                             DI Framework
├── creates AppDatabase              ├── creates AppDatabase (once)
├── creates UserDao                  ├── creates UserDao
├── creates Retrofit                 ├── creates Retrofit (once)
├── creates UserRemoteDataSource     ├── creates UserRemoteDataSource
├── creates UserRepository           ├── creates UserRepository
└── creates UserViewModel            └── delivers UserViewModel to Activity


Every screen repeats this.           One place owns construction.
Adding a dep = N file changes.       Adding a dep = 1 file change.
Testing is impossible.               Any dep can be replaced with a fake.
```

---

## Production Notes

```text
* DI is a design principle, not a framework.
  You can do manual DI by passing dependencies
  through constructors by hand.
  Frameworks (Hilt, Koin, Dagger) automate the wiring.

* The construction cascade problem grows with the codebase.
  Small apps survive manual wiring.
  Apps with ten or more screens need a framework.

* "Inversion of Control" is another name for this idea:
  a class no longer controls how its dependencies are created.
  The framework controls it instead.

* Every production Android codebase you will encounter uses Hilt.
  Understanding why DI exists matters more than memorizing Hilt's API.

* The testability win is often the deciding argument in code reviews.
  "How do you test this without hitting the network?" is a question
  that manual construction cannot answer cleanly.
```

---

## Common Misconception

A common misconception is:

```text
Dependency Injection = Using A DI Framework
```

Incorrect.

```text
Dependency Injection

↓

A Design Principle:
Objects Receive Dependencies From The Outside

---

Manual DI

↓

You write the wiring by hand.
Works. Gets tedious past ten files.

---

DI Framework (Hilt)

↓

The framework generates the wiring for you.
```

Manual DI is valid.

It is what developers do before a project grows.

Hilt automates what becomes tedious at scale.

---

## A Natural Question

We now understand why Dependency Injection exists.

Objects should receive their dependencies
rather than build them.

A natural question appears:

```text
If Manual Construction Is So Harmful —
What Specifically Breaks In A Production App?
```

That question leads us to:

```text
The Problem With Manual Construction
```

---

## Revision

### Core Idea

```text
Dependency Injection

↓

Objects Receive Their Dependencies From The Outside,
Rather Than Creating Them
```

### Mental Model

```text
Without DI                           With DI

Object builds its own deps           Object declares what it needs
Construction code spreads            Construction lives in one place
Testing is impossible                Any dep can be replaced with a fake
```

### Production Recognition

```kotlin
// Without DI
class UserViewModel : ViewModel() {
    private val repo = UserRepository(
        UserRemoteDataSource(...), db.userDao()
    )
}

// With DI
class UserViewModel(
    private val userRepository: UserRepository
) : ViewModel()
```

```text
Constructor Parameters = Declared Dependencies = Testable = Maintainable
```

### Previous Concept

```text
Data Architecture (DOC 13)
```

### Next Concept

```text
The Problem With Manual Construction
```
