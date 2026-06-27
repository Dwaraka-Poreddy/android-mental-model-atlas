# CompositionLocal

## Observation

Earlier,

we learned that a Design System defines shared values such as:

- colors,
- typography,
- spacing,
- shapes.

A natural question appears.

> **How do these values become available to every composable in the application?**

Imagine a deeply nested composable.

```text
App

↓

HomeScreen

↓

BalanceSection

↓

BalanceCard

↓

BalanceText
```

Suppose `BalanceText` needs to know the current city.

(We'll use a city instead of a theme for now because it's easier to focus on the mechanism itself.)

How does it obtain that information?

---

## The First Thought

One obvious solution is to pass the city through every composable.

```kotlin
App(city)

↓

HomeScreen(city)

↓

BalanceSection(city)

↓

BalanceCard(city)

↓

BalanceText(city)
```

Eventually,

every composable starts looking like this.

```kotlin
fun BalanceCard(

    city: String,

    ...

)
```

Notice something.

`BalanceCard` doesn't actually care about the city.

It simply forwards it to another composable.

---

## Parameter Drilling

This pattern is called **parameter drilling**.

Imagine passing a package through five people.

```text
Alice

↓

Bob

↓

Charlie

↓

David

↓

Emma
```

Suppose only Emma needs the package.

Bob,

Charlie,

and David never open it.

They simply pass it along.

Parameter drilling works exactly the same way.

Intermediate composables become messengers instead of consumers.

---

## The Bigger Problem

Now imagine we also need to pass:

- theme,
- locale,
- density,
- layout direction,
- permissions,
- window size.

Soon,

our composables become filled with parameters they never actually use.

```kotlin
fun BalanceCard(

    city,

    theme,

    locale,

    density,

    layoutDirection,

    ...

)
```

That doesn't scale very well.

A natural question appears.

> **Can we somehow make shared values available to the entire Composition Tree without passing them through every composable?**

The answer is:

**Yes.**

That's exactly what `CompositionLocal` exists to solve.

---

## Before We Learn The API...

Before we look at any code,

we need to understand one important idea.

> **What exactly is a CompositionLocal?**

This is where many developers become confused.

---

## Think Of It Like A Dictionary

Imagine a dictionary.

```text
Country

↓

India

----------------

Language

↓

English

----------------

Currency

↓

Rupee
```

Notice something.

A dictionary always stores information as:

```text
Key

↓

Value
```

The key isn't the value.

The key simply identifies **which value** we want.

For example,

the key:

```text
Language
```

is not:

```text
English
```

It merely tells us which value to retrieve.

---

## A CompositionLocal Is Very Similar

When we write:

```kotlin
val LocalCity = compositionLocalOf {

    "Hyderabad"

}
```

many developers think:

> **"LocalCity contains Hyderabad."**

Not exactly.

Instead,

it's better to think of it like this.

```text
Key

↓

LocalCity
```

`LocalCity` is simply the identifier.

It represents:

> **"The current city."**

The important thing to understand is that **the key and the value are two different things.**

---

## Then Why Did We Write "Hyderabad"?

That's an excellent question.

```kotlin
val LocalCity = compositionLocalOf {

    "Hyderabad"

}
```

doesn't mean:

> **The application's city is Hyderabad.**

Instead,

`"Hyderabad"` is simply the **default value** associated with this key.

Think of it like this.

```text
LocalCity

↓

Default Value

↓

Hyderabad
```

At this point,

we've only created the key.

Nobody has supplied a different value yet.

---

## Reading The Value

Now imagine we write:

```kotlin
@Composable
fun BalanceText() {

    Text(

        text = LocalCity.current

    )

}
```

A natural question appears.

> **Where does `LocalCity.current` get its value from?**

Let's answer that with the simplest possible situation.

Suppose nobody has provided any value.

Compose now asks:

```text
Someone Asked For

LocalCity

↓

Was A Value Provided?

↓

No

↓

Use The Default Value

↓

Hyderabad
```

So,

`BalanceText` displays:

```text
Hyderabad
```

Notice something important.

`BalanceText` never received `"Hyderabad"` as a parameter.

It simply asked:

> **"What's the current value associated with the key `LocalCity`?"**

Since nobody had supplied another value,

Compose returned the default.

---

## One Remaining Question

We've now learned:

- a CompositionLocal creates a key,
- the key has a default value,
- `.current` reads the value associated with that key.

A natural question appears.

> **If every key already has a default value, how do we change that value for a particular screen or part of the application?**

That leads us to the next concept.

**CompositionLocalProvider**

---

## Changing The Value

In the previous section,

we learned that `LocalCity` is simply a key.

We also learned that,

if nobody provides a value,

Compose returns the default.

```text
LocalCity

↓

Default Value

↓

Hyderabad
```

A natural question appears.

> **What if a particular screen wants to use a different city?**

That's where `CompositionLocalProvider` comes in.

---

## Introducing CompositionLocalProvider

Imagine we write:

```kotlin
CompositionLocalProvider(

    LocalCity provides "Bengaluru"

) {

    HomeScreen()

}
```

Notice something important.

We did **not** create another `LocalCity`.

The key already existed.

Instead,

we simply said:

> **"For everything inside this subtree, associate `LocalCity` with `Bengaluru`."**

Conceptually,

it looks like this.

```text
LocalCity

↓

Bengaluru

(For This Subtree)
```

The key didn't change.

Only the value associated with it changed.

---

## Reading The Value Again

Now imagine `BalanceText()` is inside `HomeScreen()`.

```kotlin
@Composable
fun BalanceText() {

    Text(

        text = LocalCity.current

    )

}
```

What happens now?

Compose asks:

```text
Someone Asked For

LocalCity

↓

Was A Value Provided?

↓

Yes

↓

Return The Provided Value

↓

Bengaluru
```

So,

instead of displaying:

```text
Hyderabad
```

the composable now displays:

```text
Bengaluru
```

Notice something interesting.

We didn't change `BalanceText()` at all.

The exact same code now produces a different result.

Only the environment changed.

---

## The Provider Doesn't Affect The Entire Application

Another important detail.

Imagine our application looks like this.

```text
App

├── HomeScreen

└── SettingsScreen
```

Suppose we only wrap `HomeScreen`.

```kotlin
CompositionLocalProvider(

    LocalCity provides "Bengaluru"

) {

    HomeScreen()

}

SettingsScreen()
```

Now the Composition Tree looks like this.

```text
App

├── HomeScreen

│     ↓

│  LocalCity

│     ↓

│ Bengaluru

│

└── SettingsScreen

      ↓

 Default

      ↓

 Hyderabad
```

Notice what happened.

`HomeScreen` reads:

```text
Bengaluru
```

while `SettingsScreen` still reads:

```text
Hyderabad
```

The provider only affects its own subtree.

It doesn't change the rest of the application.

---

## Thinking About It Visually

Imagine the provider creates an invisible boundary.

```text
App

│

├───────────────┐

│               │

│ Provider      │

│               │

│ HomeScreen    │

│     │         │

│ BalanceCard   │

│     │         │

│ BalanceText   │

│               │

└───────────────┘

SettingsScreen
```

Every composable inside the boundary sees:

```text
Bengaluru
```

Every composable outside the boundary continues using:

```text
Hyderabad
```

This is why we say the value is provided **to a subtree**.

---

## Multiple Providers

A natural question appears.

> **What if another provider exists inside the first one?**

Imagine this.

```kotlin
CompositionLocalProvider(

    LocalCity provides "Bengaluru"

) {

    HomeScreen()

    CompositionLocalProvider(

        LocalCity provides "Mumbai"

    ) {

        CheckoutScreen()

    }

}
```

Which value should `CheckoutScreen()` receive?

Compose always chooses the **nearest provider**.

Conceptually,

it looks like this.

```text
App

↓

Bengaluru

↓

HomeScreen

↓

Mumbai

↓

CheckoutScreen
```

`HomeScreen` reads:

```text
Bengaluru
```

`CheckoutScreen` reads:

```text
Mumbai
```

The nearest provider always wins.

---

## An Analogy

Imagine a company.

The CEO announces:

> **"Everyone works from Hyderabad."**

That's the default policy.

Later,

the manager of one department announces:

> **"Our department works from Bengaluru."**

Finally,

the lead of one smaller team announces:

> **"Our team works from Mumbai."**

Now imagine asking three employees where they work.

Someone in another department answers:

```text
Hyderabad
```

Someone in the Bengaluru department answers:

```text
Bengaluru
```

Someone in the Mumbai team answers:

```text
Mumbai
```

Everyone follows the **closest policy** that applies to them.

CompositionLocal behaves exactly the same way.

The nearest provider determines the value.

---

## If You're Coming From Flutter

If you've worked with Flutter,

this should feel very familiar.

Flutter uses `InheritedWidget` to solve the same problem.

For example,

a theme is provided like this.

```dart
Theme(

  data: myTheme,

  child: HomeScreen(),

)
```

Later,

a widget simply reads it.

```dart
final theme = Theme.of(context);
```

Compose follows the same mental model.

Instead of:

```dart
Theme.of(context)
```

we write:

```kotlin
LocalCity.current
```

or,

more commonly,

```kotlin
MaterialTheme.colorScheme
```

Both frameworks allow a parent to provide a value,

and descendants automatically read it,

without parameter drilling.

The implementations differ,

but the mental model is remarkably similar.

---

## Bringing Everything Together

```text
Create Key

↓

LocalCity

↓

(Optional)

Provide Value

↓

Bengaluru

↓

Composable Reads

↓

LocalCity.current

↓

Provider Found?

        │

   ┌────┴────┐

   │         │

  Yes       No

   │         │

   ▼         ▼

Bengaluru  Hyderabad
```

Notice how each piece has a different responsibility.

- `compositionLocalOf` creates the key.
- `CompositionLocalProvider` associates a value with that key for a subtree.
- `.current` retrieves the current value.

Each API has one clear job.

---

## One Remaining Question

We've now learned how shared values flow through the Composition Tree.

A natural question appears.

> **When should we use CompositionLocal, and when should we continue passing values explicitly as parameters?**

That leads us to the final part of this chapter.

---

## When Should We Use CompositionLocal?

At this point,

a natural question appears.

> **Should we stop passing parameters and use CompositionLocal everywhere?**

The answer is:

**No.**

In fact,

most data in your application should **not** be placed inside a `CompositionLocal`.

To understand why,

we need to distinguish between two different kinds of data.

---

## Two Kinds Of Data

Imagine a composable like this.

```kotlin
UserCard(

    user = user

)
```

The `user` determines **what** this composable displays.

Without a user,

`UserCard` cannot perform its responsibility.

Now consider another value.

```text
Current Theme
```

Does `UserCard` own the theme?

No.

The theme simply describes the environment in which the composable is being displayed.

Notice the difference.

```text
Business Data

↓

What The Composable Displays

----------------------------

Environment Data

↓

How The Composable Is Displayed
```

These two kinds of data have very different responsibilities.

---

## Business Data Should Remain Explicit

Suppose we write:

```kotlin
UserCard(

    user = user
)
```

Looking at this function call,

it's immediately obvious that `UserCard` depends on a `User`.

Its dependency is explicit.

Now imagine this instead.

```kotlin
@Composable
fun UserCard() {

    val user = LocalUser.current

}
```

At first glance,

nothing tells us that `UserCard` depends on a user.

That dependency is now hidden.

Someone reading the code has to inspect the implementation to discover it.

This makes the code harder to understand.

---

## Environment Data Is Different

Now imagine a `Text` composable.

```kotlin
Text(

    text = "Balance"

)
```

We never pass:

- theme,
- density,
- layout direction,
- locale.

Yet `Text` still renders correctly.

Why?

Because these values describe the **environment**,

not the business meaning of the screen.

Making them available through `CompositionLocal`

reduces parameter drilling

without hiding important business dependencies.

---

## A Simple Rule

Whenever you're considering a `CompositionLocal`,

ask yourself one question.

> **Does this value describe the environment, or does it describe my feature?**

If it describes the environment,

`CompositionLocal` is often a good fit.

If it describes the feature,

it usually belongs as an explicit parameter.

Think of it like this.

```text
Environment

↓

Shared By Many Composables

↓

CompositionLocal

----------------------------

Business State

↓

Specific To A Feature

↓

Explicit Parameters
```

This simple distinction prevents many architectural mistakes.

---

## Common Examples

Values that commonly belong in a `CompositionLocal` include:

- Theme
- Colors
- Typography
- Density
- Locale
- Layout Direction
- Android Context
- Lifecycle Owner

These values describe the environment in which every composable operates.

Now compare them with:

- User
- Product
- Shopping Cart
- Search Results
- Transaction History

These are business concepts.

They should usually remain explicit parameters.

---

## A Common Misconception

Some developers discover `CompositionLocal`

and become excited.

Soon,

they place everything inside it.

- ViewModels
- Repositories
- User objects
- Business state

Eventually,

every composable silently depends on hidden values.

The architecture becomes difficult to understand.

Imagine opening this composable.

```kotlin
@Composable
fun CheckoutScreen() {

    ...

}
```

What does it depend on?

You can't tell.

The dependencies are hidden.

Now compare it with:

```kotlin
CheckoutScreen(

    cart = cart,

    onCheckout = ...

)
```

The dependencies are immediately visible.

Explicit code is easier to understand,

test,

and maintain.

---

## Thinking Like A Production Engineer

Experienced Compose engineers don't ask:

> **Can I avoid passing this parameter?**

Instead,

they ask:

> **Should this dependency be visible?**

If the answer is yes,

keep it as a parameter.

If the answer is no,

and it's truly part of the application's environment,

`CompositionLocal` may be the right choice.

---

## Connecting Back To The Previous Chapter

Earlier,

we learned that a Design System is the single source of truth for design decisions.

A natural question appeared.

> **How do those shared decisions become available to every composable?**

Now we know the answer.

Compose uses `CompositionLocal` to distribute those shared environmental values throughout the Composition Tree.

Theming is simply one application of this mechanism.

---

## Bringing Everything Together

```text
Shared Value

↓

Is It Business Data?

        │

   ┌────┴────┐

   │         │

  Yes       No

   │         │

   ▼         ▼

Pass As     Shared Environment?

Parameter          │

              ┌────┴────┐

              │         │

             Yes       No

              │         │

              ▼         ▼

     CompositionLocal  Reconsider
```

Notice that `CompositionLocal` isn't trying to replace parameters.

It's solving a different problem.

---

## Revision

### Core Idea

`CompositionLocal` allows shared environmental values to flow through the Composition Tree,

eliminating parameter drilling for information that many composables need.

---

### Mental Model

```text
compositionLocalOf

↓

Creates A Key

↓

CompositionLocalProvider

↓

Associates A Value

With That Key

↓

.current

↓

Retrieves The Current Value
```

Each API has one clear responsibility.

---

### Engineering Mindset

Don't think:

> **"CompositionLocal replaces parameters."**

Think:

> **"CompositionLocal distributes shared environment values."**

Business data should remain explicit.

Environment data can often be implicit.

---

### Key Insight

The biggest purpose of `CompositionLocal` isn't avoiding long parameter lists.

It's allowing the Composition Tree to naturally distribute shared environmental information to every composable that needs it.

---

## One Remaining Question

We've now learned **how** shared values flow through the Composition Tree.

A natural question appears.

> **How does Jetpack Compose use this mechanism to distribute colors, typography, shapes, and every other design decision across an application?**

That leads us to the next concept.

**Theming**
