# Stability Annotations (`@Immutable`, `@Stable`)

## Observation

Earlier in the Atlas,

we learned that the Compose Runtime performs **stability inference**.

Most of the time,

Compose automatically determines whether a type is stable.

A natural question appears.

> **If Compose already figures this out automatically, why do `@Immutable` and `@Stable` exist?**

---

## The First Thought

At first,

these annotations seem unnecessary.

If the compiler is already smart enough,

why would developers ever need to help it?

---

## The Reality

Most of the time,

you don't.

Suppose your model looks like this.

```kotlin
data class User(

    val name: String,

    val age: Int

)
```

Compose can usually infer that this type behaves predictably.

No annotations are required.

For the majority of applications,

this is exactly how you'll write your models.

---

## When Does The Compiler Struggle?

The compiler can only reason about what it can see.

Sometimes,

that isn't enough.

Imagine a class with:

- custom getters,
- delegated properties,
- complex internal logic,
- types from external libraries,
- behavior that can't be fully analyzed.

Eventually,

the compiler reaches a point where it has to admit:

> **"I don't know whether this type is safe to optimize."**

Earlier,

we learned what Compose does in that situation.

```text
Not Sure

↓

Choose Correctness

↓

Treat As Unstable
```

Compose always prefers correctness over performance.

---

## So What Are These Annotations?

This is the most important idea in this chapter.

`@Immutable` and `@Stable` are **not optimization annotations**.

They are **contracts**.

When you write:

```kotlin
@Immutable
```

you are **not** asking Compose to optimize your code.

You are telling Compose:

> **"You can trust me."**

That is a huge difference.

---

## Why Is That Dangerous?

Because Compose believes you.

If your annotation is incorrect,

Compose won't magically detect the mistake.

It will optimize based on the promise you made.

That can lead to:

- skipped recompositions,
- stale UI,
- subtle bugs,
- inconsistent behavior.

The compiler can't protect you anymore.

You explicitly told it:

> **"I guarantee this type satisfies the contract."**

---

## An Analogy

Imagine signing a legal document.

Your signature doesn't make the document true.

It simply declares:

> **"I confirm this information is correct."**

If the information is wrong,

the signature doesn't save you.

It makes you responsible.

`@Immutable` and `@Stable` work exactly the same way.

The annotation doesn't make your class stable.

It declares that your class already satisfies the required contract.

---

# Understanding `@Immutable`

`@Immutable` communicates a very strong guarantee.

It tells Compose:

> **This object's observable state will never change after it is created.**

That means the runtime can safely assume:

- existing values won't mutate,
- properties won't unexpectedly change,
- the object behaves like a snapshot of state.

Notice something important.

The annotation doesn't make the object immutable.

The object must already be immutable.

The annotation simply communicates that fact to the runtime.

---

## Understanding `@Stable`

`@Stable` is more subtle.

It does **not** mean the object never changes.

Instead,

it means:

> **If the object's observable state changes, Compose will always be able to observe those changes correctly.**

In other words,

the object behaves predictably.

Changes don't happen silently.

Compose never loses track of them.

That's a very different promise from `@Immutable`.

---

## A Practical Example

Imagine this class.

```kotlin
class Counter {

    var count by mutableStateOf(0)

}
```

The object itself is mutable.

Its state changes over time.

But every change happens through `mutableStateOf`.

Compose already knows whenever `count` changes.

Nothing happens silently.

The object behaves predictably.

That's the kind of situation `@Stable` is designed for.

---

## Comparing The Two

Think about the guarantees each annotation provides.

### `@Immutable`

```text
Object Never Changes

↓

Compose Can Trust

Nothing Will Change
```

---

### `@Stable`

```text
Object May Change

↓

But Every Observable Change

Will Be Properly Reported

↓

Compose Can Still Trust It
```

One guarantees **no mutation**.

The other guarantees **predictable mutation**.

---

## When Should I Use These?

For most developers,

the answer is surprisingly simple.

> **Almost never.**

Compose's stability inference is already very good.

If your models are well-designed,

the compiler usually infers stability automatically.

You should only consider these annotations when:

- you've investigated a real performance issue,
- you've confirmed that inference isn't sufficient,
- and you fully understand the contract you're making.

These are advanced tools,

not everyday annotations.

---

## The Dangerous Anti-pattern

Imagine the compiler reports:

```text
Unstable parameter
```

A developer immediately writes:

```kotlin
@Stable
class User
```

Problem solved?

Not at all.

The warning disappeared.

The underlying problem didn't.

It's like seeing:

```text
Check Engine
```

and placing tape over the warning light.

The engine still has a problem.

The annotation simply prevented the warning from being visible.

Never use these annotations to silence diagnostics.

Use them only when the contract is genuinely true.

---

## Thinking Like A Production Engineer

Whenever you're tempted to add one of these annotations,

ask yourself:

> **Am I helping the compiler understand something it genuinely couldn't infer?**

Or...

> **Am I trying to silence a warning?**

Those are completely different motivations.

The first is valid.

The second is dangerous.

---

## Connecting Back To Doc 7

Earlier,

we learned that Compose uses stability to decide whether recomposition can be skipped.

This chapter adds an important detail.

Most of the time,

Compose figures stability out automatically.

Only in exceptional situations do developers explicitly provide additional information.

```text
Compose Can't Infer

↓

Developer Knows More

↓

Provide Contract

↓

Compose Can Trust
```

Notice what changed.

We're not changing runtime behavior.

We're providing additional knowledge to the runtime.

---

## Bringing Everything Together

```text
Compose Performs

Stability Inference

↓

Was The Type

Understood?

        │

   ┌────┴────┐

   │         │

  Yes       No

   │         │

   ▼         ▼

Infer      Developer May

Stability   Provide Contract

                 │

                 ▼

         @Immutable

         @Stable

                 │

                 ▼

      Runtime Trusts

      The Contract
```

The important point isn't the annotation.

It's the trust relationship between the developer and the runtime.

---

## Revision

### Core Idea

`@Immutable` and `@Stable` don't make a type stable.

They communicate stability guarantees that the developer promises are already true.

---

### Engineering Mindset

Don't think:

> **"These annotations improve performance."**

Think:

> **"These annotations tell the runtime what I guarantee about this type."**

Performance is simply the consequence of accurate guarantees.

---

### Key Insight

Most applications never need these annotations.

Compose's stability inference is already excellent.

Use them only when the compiler genuinely lacks enough information and you fully understand the contract you're making.

---

## One Remaining Question

We've now learned how stable models help the Compose Runtime decide whether recomposition can be skipped.

A natural question appears.

> **When displaying hundreds or thousands of items, how does Compose know which individual item should be preserved and which one should be recreated?**

That leads us to the next concept.

**Lazy List Item Keys**
