# runBlocking

## A Natural Assumption

So far we have learned:

```kotlin
suspend fun loadUser() {
    println("Loading User")
}
```

A natural assumption is:

```kotlin
fun main() {
    loadUser()
}
```

This feels reasonable because:

```text
loadUser()
Looks Like A Normal Function
```

However, this does not compile.

---

## The Surprise

The compiler reports an error.

Conceptually:

```text
Suspend Functions Cannot Be Called Like Normal Functions
```

A suspend function can only be called from:

```text
Another Suspend Function
OR
A Coroutine
```

Examples:

```kotlin
suspend fun loadScreen() {
    loadUser()
}
```

Valid.

---

```kotlin
launch {
    loadUser()
}
```

Valid.

---

```kotlin
fun main() {
    loadUser()
}
```

Invalid.

---

## Why Does This Restriction Exist?

To answer this, we need to remember what we learned earlier.

A suspend function may:

```text
Suspend

Save Continuation

Resume Later
```

For example:

```kotlin
suspend fun loadUser() {
    delay(1000)
}
```

Conceptually:

```text
Start Function
↓
Suspend
↓
Save Continuation
↓
Resume Later
↓
Continue Execution
```

Regular functions do not have the machinery required to support this behavior.

Therefore:

```text
Suspend Functions Require Coroutine Machinery
```

This is why Kotlin prevents them from being called from ordinary code.

---

## A Bigger Problem Appears

Now a natural question appears:

```text
If Suspend Functions Require Coroutines...

How Does The First Coroutine Start?
```

Consider:

```kotlin
launch {
    loadUser()
}
```

We already know:

```text
launch Creates A Coroutine
```

But another question immediately appears:

```text
Who Called launch?
```

At some point, execution must begin from:

```text
Regular Kotlin Code
```

This is the problem that:

```text
runBlocking
```

solves.

---

## Enter runBlocking

Example:

```kotlin
fun main() {
    runBlocking {
        loadUser()
    }
}
```

Conceptually:

```text
Regular Kotlin Code
↓
runBlocking
↓
Coroutine World
```

`runBlocking` acts as a bridge between:

```text
Regular Code
And
Coroutine Code
```

---

## What Does runBlocking Do?

Conceptually:

```text
runBlocking
```

does two things:

```text
Creates A Coroutine
AND
Blocks The Current Thread Until That Coroutine Finishes
```

This is why it is called:

```text
runBlocking
```

---

## First Mental Model

Think of:

```text
launch
```

as:

```text
Start Work
And Leave
```

---

Think of:

```text
runBlocking
```

as:

```text
Start Work
Wait Until Finished
Then Continue
```

---

## launch vs runBlocking

### launch

Conceptually:

```text
Create Coroutine
↓
Return Immediately
```

Example:

```kotlin
launch {
    loadUser()
}
```

The caller continues immediately.

---

### runBlocking

Conceptually:

```text
Create Coroutine
↓
Wait For Completion
↓
Return
```

Example:

```kotlin
runBlocking {
    loadUser()
}
```

The caller does not continue until the coroutine finishes.

---

## Why Does runBlocking Block?

Remember:

```text
launch
```

creates a coroutine and immediately returns.

That behavior works well when:

```text
A Program Is Already Running
```

However, every program must eventually start from:

```text
Regular Kotlin Code
```

If coroutine execution could begin and the caller immediately continue, the program might finish before the coroutine work completes.

`runBlocking` prevents this by preventing the current thread from continuing past the runBlocking call until the coroutine inside it has finished.

---

## Execution Timeline

Consider:

```kotlin
fun main() {

    runBlocking {

        loadUser()

        println("User Loaded")
    }

    println("Program Finished")
}
```

Let's execute this step by step.

---

### Step 1

Execution enters:

```kotlin
runBlocking {
```

Current state:

```text
Main Thread Running
```

---

### Step 2

`runBlocking` creates a coroutine.

State:

```text
Main Thread Running

Coroutine #1 Running
```

---

### Step 3

Coroutine #1 executes:

```kotlin
loadUser()
```

---

### Step 4

Suppose:

```kotlin
suspend fun loadUser() {

    delay(1000)

    println("Loading Complete")
}
```

Execution reaches:

```kotlin
delay(1000)
```

The coroutine suspends.

Conceptually:

```text
Coroutine Suspended

Continuation Saved
```

At this point, a natural question appears:

```text
If The Coroutine Has Suspended,

What Is The Thread Doing Right Now?
```

This is an excellent question.

The answer depends on concepts we have not yet learned:

```text
Coroutine Context

Dispatchers

Schedulers
```

For now, focus on one idea:

```text
Coroutine Suspended ≠ Coroutine Finished
```

The coroutine has temporarily stopped progressing and will resume later using its saved continuation.

We will discuss what happens to the underlying thread in a future chapter.

Remember:

```text
Suspension ≠ Thread Blocking
```

The coroutine has paused its execution.

Its continuation remembers where execution should resume later.

---

### Step 5

After one second:

```text
Coroutine Resumed
```

Execution continues from:

```kotlin
println("Loading Complete")
```

Output:

```text
Loading Complete
```

---

### Step 6

`loadUser()` finishes.

Execution continues:

```kotlin
println("User Loaded")
```

Output:

```text
User Loaded
```

---

### Step 7

Coroutine #1 finishes.

State:

```text
Coroutine #1 Finished
```

---

### Step 8

Only now does:

```text
runBlocking
```

return.

The thread is finally allowed to continue.

---

### Step 9

Execution continues:

```kotlin
println("Program Finished")
```

Output:

```text
Program Finished
```

---

## The Most Important Behavior

Consider:

```kotlin
fun main() {

    runBlocking {

        delay(5000)
    }

    println("Done")
}
```

Many developers initially expect:

```text
Start Coroutine

Done

Coroutine Finishes Later
```

But that is NOT what happens.

Instead:

```text
Start Coroutine

Coroutine Finishes

Done
```

because:

```text
runBlocking Does Not Return Until Its Coroutine Finishes
```

This is the defining behavior of `runBlocking`.

---

## What Is Actually Blocking?

A common question is:

```text
What Exactly Gets Blocked?
```

The answer is:

```text
The Current Thread
```

Not:

```text
The Coroutine
```

Inside:

```kotlin
runBlocking {

    delay(1000)
}
```

the coroutine can still:

```text
Suspend

Resume

Continue Execution
```

The coroutine system still behaves normally.

What changes is:

```text
The Thread That Called runBlocking Cannot Continue Past runBlocking Until The Coroutine Finishes
```

---

## Important Clarification

A common misconception:

```text
runBlocking = Coroutine
```

Incorrect.

`runBlocking` is a function.

It creates a coroutine and manages its execution.

Just as:

```text
launch
```

is not a coroutine,

```text
runBlocking
```

is not a coroutine.

---

## When Is runBlocking Commonly Used?

Typical use cases:

```text
Program Entry Points

Learning Examples

Tests

Small Utility Programs
```

It is usually NOT used inside:

```text
Android UI Code

Production Application Logic
```

because blocking threads is generally undesirable there.

---

## Common Misconceptions

### Misconception 1

```text
runBlocking Removes Suspension
```

Incorrect.

Coroutines can still suspend and resume normally.

---

### Misconception 2

```text
runBlocking Is Just Another Name For launch
```

Incorrect.

```text
launch
↓
Returns Immediately

runBlocking
↓
Waits For Completion
```

---

### Misconception 3

```text
runBlocking Creates A Thread
```

Incorrect.

Its primary purpose is:

```text
Create Coroutine

Block Current Thread Until Completion
```

---

### Misconception 4

```text
Suspend Functions Can Be Called Anywhere
```

Incorrect.

Suspend functions require:

```text
Another Suspend Function

OR

A Coroutine
```

---

## The Key Mental Model

Think of:

```text
runBlocking
```

as:

```text
A Bridge
```

between:

```text
Regular Kotlin Code

And

Coroutine Code
```

It allows coroutine execution to begin from ordinary code while ensuring the caller does not continue until the coroutine work has completed.

---

## Revision Table

| Section | Content |
|----------|----------|
| Type | Function |
| Main Purpose | Bridge regular code and coroutine code |
| Creates Coroutine? | Yes |
| Blocks Current Thread? | Yes |
| Returns Immediately? | No |
| Supports Suspension? | Yes |
| Common Usage | Entry points, tests, examples |
| Previous Concept | coroutineScope Builder |

---

## Questions We Are Intentionally Postponing

You may naturally wonder:

```text
How Does runBlocking Create A Coroutine?

What Exactly Is A Coroutine Context?

How Does Kotlin Decide Which Thread Runs A Coroutine?

What Happens If The Coroutine Fails?
```

These are excellent questions.

However, they require concepts we have not yet learned:

```text
Coroutine Context

Dispatchers

Exception Handling
```

We will answer them in future chapters.

For this chapter, focus on one idea:

```text
runBlocking Allows Coroutine Execution To Start From Regular Kotlin Code
```
