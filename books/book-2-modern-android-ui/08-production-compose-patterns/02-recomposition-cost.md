# Recomposition Cost

## Observation

In the previous chapter,

we learned that Compose minimizes runtime work by recomposing only the necessary boundaries.

A natural question appears.

> **If Compose already recomposes so little, what actually makes a Compose screen slow?**

The answer is surprising.

Most of the time,

it's not recomposition itself.

It's the work we perform **during** recomposition.

---

## A Simple Example

Consider this composable.

```kotlin
@Composable
fun UserName(
    name: String
) {
    Text(name)
}
```

Suppose `name` changes.

Compose recomposes `UserName()`.

How expensive was that recomposition?

Not very.

Compose executes one composable,

updates one `Text`,

and finishes.

This is exactly the kind of work Compose is designed to perform efficiently.

---

## When Cost Starts Appearing

Now consider a different example.

```kotlin
@Composable
fun UserScreen(
    users: List<User>
) {

    val groupedUsers =
        users
            .groupBy { it.country }
            .toSortedMap()

    UserList(groupedUsers)
}
```

Imagine this screen recomposes 30 times.

Ask yourself.

What work is happening 30 times?

Compose isn't doing anything unusual.

**Your code is.**

Every recomposition performs:

- grouping
- sorting
- object allocation

Compose successfully minimized its own work,

but your composable still performs expensive computations every time it executes.

---

## Another Production Example

```kotlin
@Composable
fun ChatScreen(
    messages: List<Message>
) {

    val unreadCount =
        messages.count { !it.read }

    Header(
        unreadCount = unreadCount
    )

    MessageList(messages)
}
```

Now suppose the screen recomposes because a typing indicator changes.

The messages haven't changed.

The unread count hasn't changed.

Yet this line still executes.

```kotlin
messages.count { !it.read }
```

With ten messages,

that's insignificant.

With twenty thousand messages,

it becomes unnecessary work during every recomposition.

Compose didn't make recomposition expensive.

Our implementation did.

---

## Thinking Like A Production Engineer

A beginner often asks:

> **"Will this composable recompose?"**

An experienced Compose developer asks:

> **"If this composable recomposes 100 times, is the work inside still cheap?"**

That is a much more useful engineering question.

---

## Common Sources Of Recomposition Cost

When reviewing Compose code,

look for work like this inside composables:

```kotlin
val grouped = users.groupBy { ... }

val sorted = users.sortedBy { ... }

val filtered = users.filter { ... }

val formatted =
    DateTimeFormatter(...)
        .format(date)

val recommendations =
    recommendationEngine.generate(users)
```

None of these are Compose APIs.

They're simply computations that now execute as part of recomposition.

Sometimes that's perfectly acceptable.

Sometimes it's the reason a screen becomes slow.

Learning to tell the difference is an important engineering skill.

---

## Does This Mean Composables Should Contain No Logic?

No.

That's another misconception.

Simple work is perfectly fine.

For example,

this is completely reasonable.

```kotlin
@Composable
fun Greeting(
    user: User
) {
    Text("Hello ${user.name}")
}
```

Likewise,

small calculations are usually not worth optimizing.

```kotlin
val fullName =
    "${user.firstName} ${user.lastName}"
```

Optimization only becomes worthwhile when:

- the computation is expensive,
- the data set is large,
- or the recomposition happens frequently.

The goal isn't to eliminate all work.

The goal is to eliminate **unnecessary expensive work**.

---

## How Do We Fix Expensive Recomposition?

Depending on the situation,

there are several options.

Sometimes we should:

- move the computation into the ViewModel,
- cache the result,
- derive it only when necessary,
- improve recomposition boundaries,
- or use Compose's state helper APIs.

We'll study each of these techniques throughout the rest of this document.

For now,

the important lesson is learning to recognize the problem.

---

## Production Recognition

During a code review,

when you see something like this:

```kotlin
@Composable
fun FeedScreen(...) {

    // Expensive computation

    ...

}
```

don't immediately ask:

> **"Will this recompose?"**

Instead ask:

> **"If this recomposes frequently, is the work inside still appropriate?"**

That simple shift in thinking catches many performance issues before they ever reach production.

---

## Revision

### Core Idea

```text
State Changed
      │
      ▼
Composable Executes
      │
      ▼
Application Code Executes
      │
      ▼
How Expensive Is That Work?
```

### Engineering Mindset

Don't optimize for **fewer recompositions**.

Optimize for **cheaper recompositions**.

### Key Insight

Compose usually isn't the bottleneck.

The work inside your composables often is.

---

## One Remaining Question

We've identified that expensive work inside a composable can hurt performance.

A natural question appears.

> **How can we structure state and computations so that expensive work doesn't run on every recomposition?**

That leads us into the next part of the document.

**State Architecture**
