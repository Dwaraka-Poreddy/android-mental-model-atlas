# coroutineScope Builder

## What Problem Were We Facing?

In the previous chapter, we learned:

```text
Structured Concurrency
↓
Related Coroutines Should Stay Related
```

This immediately raises an important question:

```text
How Does Kotlin Actually Ensure That?
```

Understanding the principle is useful.

But eventually we need a way to express:

```text
One Operation
├─ Task A
├─ Task B
└─ Task C
```

in real code.

This is where:

```text
coroutineScope
```

comes in.

---

## Abstraction Level

In this chapter we are discussing:

```text
How Kotlin Keeps Related Coroutine Work Together
```

We are NOT yet discussing:

```text
Dispatchers

Cancellation

SupervisorJob

Exception Handling
```

Those topics will come later.

For now, we are focused on:

```text
What Problem coroutineScope Solves
How It Solves It
```

---

## Important Clarification

A common source of confusion is:

```text
CoroutineScope
vs
coroutineScope
```

These are different concepts.

### CoroutineScope

```text
Concept / Type
```

Represents:

```text
Ownership And Lifecycle
```

We studied this earlier.

### coroutineScope

```text
Builder Function
```

Used to keep related coroutine work together.

This chapter is about:

```text
coroutineScope
```

the builder function.

---

## First Intuition

Imagine a function:

```text
Load Screen
```

To load the screen we need:

```text
Load User

Load Feed

Load Notifications
```

These are separate tasks.

However:

```text
The Screen Is Not Ready Until All Of Them Finish
```

Conceptually:

```text
Load Screen
├─ Load User
├─ Load Feed
└─ Load Notifications
```

This is one operation composed of multiple smaller operations.

---

## The Core Problem

Suppose we start multiple pieces of work:

```text
Load Screen
├─ Load User
└─ Load Feed
```

A natural question appears:

```text
When Is Load Screen Actually Finished?
```

Is it finished when:

```text
Load User
```

finishes?

Or when:

```text
Load Feed
```

finishes?

Or immediately after starting both?

The relationship is unclear.

The parent operation and child operations no longer feel connected.

---

## What Kotlin Wants

Kotlin wants:

```text
Load Screen
```

to represent:

```text
The Entire Screen Loading Process
```

not:

```text
Start Some Work And Leave
```

The parent operation should remain connected to the child operations it creates.

---

## Enter coroutineScope

This is exactly what:

```text
coroutineScope
```

helps achieve.

Conceptually:

```text
Load Screen
└─ coroutineScope
   ├─ Load User
   └─ Load Feed
```

Now these tasks belong to the same logical unit of work.

---

## How Do Tasks Become Part Of The Scope?

A natural question is:

```text
How Does coroutineScope Know Which Tasks Belong To It?
```

Conceptually:

```text
coroutineScope Starts
↓
Creates A Group
```

Any child coroutine created inside:

```text
coroutineScope { ... }
```

automatically becomes part of that group.

Conceptually:

```text
coroutineScope
├─ Child Coroutine A
├─ Child Coroutine B
└─ Child Coroutine C
```

The scope keeps track of all child coroutines created within it.

---

## What Actually Happens?

This is the most important part of the chapter.

Suppose:

```kotlin
coroutineScope {

    launch {
        loadUser()
    }

    launch {
        loadFeed()
    }
}
```

Conceptually:

```text
coroutineScope Starts
│
├─ Child A Created
├─ Child B Created
│
├─ Child A Running
├─ Child B Running
```

At this point:

```text
coroutineScope Is Still Active
```

because child work is still running.

Suppose:

```text
Child A Finishes
```

What happens?

```text
coroutineScope Still Active
```

because:

```text
Child B Still Running
```

Later:

```text
Child B Finishes
```

Now:

```text
All Child Work Has Finished
```

Only then:

```text
coroutineScope Finishes
↓
Execution Continues
```

Conceptually:

```text
coroutineScope Starts
│
├─ Child A Running
├─ Child B Running
│
├─ Child A Finished
│
├─ Child B Finished
│
└─ coroutineScope Finished
```

This is how Kotlin keeps related work together.

---

## The Key Mental Model

Think of:

```text
coroutineScope
```

as:

```text
A Group For Related Work
```

The group remains active until:

```text
Every Child Coroutine Inside The Group Finishes
```

---

## The Most Important Behavior

Suppose:

```kotlin
coroutineScope {

    launch {
        loadUser()
    }

    launch {
        loadFeed()
    }
}

println("Screen Loaded")
```

Many developers initially expect:

```text
Start Child A

Start Child B

Screen Loaded

Child A Finished

Child B Finished
```

But that is NOT what happens.

Instead:

```text
Start Child A

Start Child B

Child A Finished

Child B Finished

Screen Loaded
```

because:

```text
coroutineScope Does Not Finish Until All Child Work Finishes
```

This behavior is what allows:

```text
Structured Concurrency
```

to work in practice.

---

## Does coroutineScope Run Coroutines Sequentially?

A common question is:

```text
Do Child Coroutines Run One By One?
```

No.

`coroutineScope` does not force sequential execution.

Conceptually:

```text
coroutineScope
├─ Child A
├─ Child B
└─ Child C
```

The child coroutines may make progress independently.

The responsibility of:

```text
coroutineScope
```

is NOT:

```text
Scheduling Work
```

Its responsibility is:

```text
Tracking The Group

Knowing When The Group Is Finished
```

---

## Minimal Example

```kotlin
suspend fun loadScreen() {

    coroutineScope {

        launch {
            loadUser()
        }

        launch {
            loadFeed()
        }
    }

    println("Screen Loaded")
}
```

Conceptually:

```text
Load User
↓
Finished

Load Feed
↓
Finished

Screen Loaded
```

The code after:

```text
coroutineScope
```

runs only after all child work inside the scope has completed.

---

## Important Clarification

A common misconception:

```text
coroutineScope Creates Threads
```

Incorrect.

This concept is about:

```text
Relationships

Grouping

Logical Units Of Work
```

Thread selection is a separate topic.

---

## Questions We Are Intentionally Postponing

You may naturally wonder:

```text
Do These Coroutines Actually Run Concurrently?

How Does Kotlin Schedule Them?

What Happens If One Child Fails?

What Happens To The Other Children?

Which Thread Executes Them?
```

These are excellent questions.

However, they require concepts we have not yet learned:

```text
Dispatchers

Cancellation

SupervisorJob

Exception Handling
```

We will answer them in future chapters.

For this chapter, focus on one idea:

```text
coroutineScope Keeps Related Coroutine Work Together As One Operation
```

---

## Why Is coroutineScope Important?

Structured Concurrency is a design principle.

Eventually Kotlin needs a mechanism to enforce that principle.

One of the primary mechanisms is:

```text
coroutineScope
```

which keeps parent work connected to child work.

Conceptually:

```text
Structured Concurrency
↓
Implemented Using coroutineScope
```

---

## Common Misconceptions

### Misconception 1

```text
coroutineScope = CoroutineScope
```

Incorrect.

One is a concept/type.

The other is a builder function.

---

### Misconception 2

```text
coroutineScope Creates Threads
```

Incorrect.

Thread management is a separate concern.

---

### Misconception 3

```text
coroutineScope Controls Execution Order
```

Incorrect.

It tracks related work.

It does not schedule that work.

---

### Misconception 4

```text
coroutineScope Is Structured Concurrency
```

Incorrect.

Structured Concurrency is the principle.

`coroutineScope` is one tool that helps implement that principle.

---

## Revision Table

| Section | Content |
|----------|----------|
| Type | Builder Function |
| Main Purpose | Keep related coroutine work together |
| Mental Model | Group for related work |
| Key Behavior | Waits for all child work to finish |
| Controls Scheduling? | No |
| Creates Threads? | No |
| Related Concept | Structured Concurrency |
| Same As CoroutineScope? | No |
| Previous Concept | Structured Concurrency |

---

## Future Exploration

- runBlocking
- Cancellation
- SupervisorJob
- Exception Handling
- Dispatchers
