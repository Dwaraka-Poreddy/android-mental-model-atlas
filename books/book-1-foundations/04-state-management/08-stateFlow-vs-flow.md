# StateFlow vs Flow

## Looking Back

Throughout the previous chapters we built two mental models.

```text
Flow

↓

Values Move
```

and

```text
StateFlow

↓

State Is Represented
```

Since both can be collected and both deliver values over time, a natural question appears:

```text
When Should We Use Flow

And

When Should We Use StateFlow?
```

---

## They Solve Different Problems

The most important difference is not:

```text
Hot

vs

Cold
```

or

```text
Initial Value

vs

No Initial Value
```

Those are implementation details.

The more fundamental difference is:

```text
What

They Represent
```

---

## Flow

Flow represents:

```text
Values Moving Through A Pipeline
```

As values are produced:

```text
A

↓

B

↓

C

↓

D
```

collectors react to those values.

The focus is on:

```text
What Arrives Next
```

---

## StateFlow

StateFlow represents:

```text
The Current Situation
```

while continuing to notify observers whenever that situation changes.

Conceptually:

```text
Current State

↓

Updated State

↓

Updated State
```

The focus is on:

```text
What Is True Right Now
```

---

## Android Examples

Repository APIs often expose Flow because they naturally produce values over time.

```kotlin
fun searchUsers(
    query: String
): Flow<List<User>>
```

Every search produces new results.

The consumer reacts whenever new values arrive.

---

UI state is typically exposed as StateFlow.

```kotlin
val uiState: StateFlow<SearchUiState>
```

The UI always needs access to:

```text
The Current Screen State
```

while continuing to observe future changes.

---

## Another Perspective

Flow naturally answers:

```text
What Value Arrived Next?
```

StateFlow naturally answers:

```text
What Is The Current State?

And

How Does It Change?
```

---

## StateFlow Is Still A Flow

StateFlow follows the same reactive programming model.

It is collected in exactly the same way.

```kotlin
viewModel.uiState.collect { state ->

    println(state)

}
```

The difference is not:

```text
How We Observe It
```

The difference is:

```text
What It Represents
```

---

## A Simple Decision Rule

Whenever you are choosing between Flow and StateFlow, ask:

```text
Am I Representing State?
```

If the answer is:

```text
Yes
```

choose:

```text
StateFlow
```

Otherwise, if you are simply reacting to values as they are produced over time, choose:

```text
Flow
```

---

## Putting Everything Together

```text
Flow

↓

Represents Values Moving Through A Pipeline

↓

Focuses On What Arrives Next

------------------------------------------------

StateFlow

↓

Represents The Current State

↓

Focuses On What Is True Right Now
```

---

## The Key Mental Model

The distinction is not about APIs.

It is about intent.

```text
Flow

↓

Values

-------------------------

StateFlow

↓

State
```

Once the kind of information is identified, the choice naturally follows.

---

## Revision Table

| Section | Content |
|----------|----------|
| Core Idea | Flow and StateFlow represent different kinds of information |
| Flow | Values moving through a pipeline |
| StateFlow | Current state with future updates |
| Android Examples | Repository API, UI State |
| Decision Rule | State → StateFlow, Values → Flow |
| Previous Concept | MutableStateFlow |
| Next Concept | SharedFlow |
