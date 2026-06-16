# Reducing Noise

## A Question From The Previous Chapter

In the previous chapter we learned:

```text
filter()
```

allows us to remove:

```text
Unwanted Values
```

from a Flow.

A natural question appears:

```text
What If The Values Themselves Are Not The Problem?
```

Suppose every value is valid.

Suppose every value is useful.

Yet reacting to every value still creates problems.

Here:

```text
Reacting
```

simply means:

```text
Doing Something Because A New Value Arrived
```

Examples include:

```text
Updating UI

Making A Network Request

Saving Data

Running Business Logic
```

Imagine a search box:

```text
H
↓
He
↓
Hel
↓
Hell
↓
Hello
```

Every value is meaningful.

None of them should be filtered out.

However:

```text
Do We Really Want To React To Every Keystroke?
```

Or would it be better to wait until the user pauses typing?

This introduces a different problem.

Not:

```text
Which Values Should Survive?
```

but:

```text
When Should We React To Values?
```

This chapter answers that question.

---

## A Different Kind Of Problem

So far we have seen:

```text
map()
↓
Changes Values
```

and:

```text
filter()
↓
Removes Values
```

Both focus on:

```text
The Data Itself
```

---

However, sometimes:

```text
The Data Is Fine
```

The problem is:

```text
How Frequently It Arrives
```

---

Consider:

```text
H
↓
He
↓
Hel
↓
Hell
↓
Hello
```

Every value is valid.

The issue is:

```text
Too Many Reactions
```

in a short period of time.

---

## A Real Android Example

Imagine:

```kotlin
searchQueryFlow.collect { query ->

    searchApi.search(query)
}
```

Suppose the user types:

```text
Hello
```

---

Conceptually:

```text
H
↓
Network Request
```

---

```text
He
↓
Network Request
```

---

```text
Hel
↓
Network Request
```

---

```text
Hell
↓
Network Request
```

---

```text
Hello
↓
Network Request
```

The result is:

```text
Five Requests
```

for what was really:

```text
One Search
```

This is often wasteful.

---

## The Missing Tool

Flow provides:

```text
debounce()
```

for this situation.

Think of:

```text
debounce()
```

as:

```text
Wait Until Things Become Quiet
```

before reacting.

---

Conceptually:

```text
Value Arrives
↓
Wait
```

---

If another value arrives:

```text
Restart Waiting
```

---

Only when no new values arrive for a period of time:

```text
Continue
```

---

## First Example

Consider:

```kotlin
searchQueryFlow
    .debounce(500)
```

Conceptually:

```text
H
↓
Wait
```

---

```text
He
↓
Restart Wait
```

---

```text
Hel
↓
Restart Wait
```

---

```text
Hell
↓
Restart Wait
```

---

```text
Hello
↓
Wait
↓
No New Input
↓
Continue
```

The collector eventually receives:

```text
Hello
```

instead of receiving every intermediate value.

---

## What Does debounce() Do?

Think of:

```text
debounce()
```

as asking:

```text
Has Anything New Arrived Recently?
```

---

If:

```text
Yes
```

then:

```text
Keep Waiting
```

---

If:

```text
No
```

then:

```text
Allow The Latest Value To Continue
```

---

The goal is not to remove bad data.

The goal is to avoid reacting too quickly.

---

## First Mental Model

Imagine a conversation.

Someone says:

```text
Hel...
```

---

Then:

```text
Hello...
```

---

Then:

```text
Hello Wo...
```

---

Then:

```text
Hello World
```

Would you respond after every partial sentence?

Usually:

```text
No
```

You wait until they finish speaking.

---

Conceptually:

```text
Talking
↓
Talking
↓
Talking
↓
Pause
↓
Respond
```

---

This is very similar to:

```text
debounce()
```

---

## Another Real Android Example

Imagine:

```kotlin
searchQueryFlow
    .debounce(500)
    .collect { query ->

        searchApi.search(query)
    }
```

Now:

```text
H

He

Hel

Hell

Hello
```

does not automatically trigger:

```text
Five Searches
```

---

Instead:

```text
User Stops Typing
↓
Hello
↓
Search Executes
```

This is one of the most common uses of:

```text
debounce()
```

in Android applications.

---

## debounce() vs filter()

At first glance both may appear to reduce values.

However they solve different problems.

---

Conceptually:

```text
filter()
↓
Should This Value Continue?
```

---

```text
debounce()
↓
Should We React To This Value Yet?
```

---

Example:

```text
Spam Message
↓
filter()
↓
Removed
```

---

Example:

```text
Search Query
↓
debounce()
↓
Wait For Typing To Stop
```

The value itself remains useful.

The timing is what changes.

---

## Relationship To The Pipeline

Earlier we learned:

```text
Producer
↓
Pipeline
↓
Collector
```

Now we can refine that model.

---

Conceptually:

```text
Producer
↓
debounce()
↓
Collector
```

---

Values continue moving through the pipeline.

However:

```text
Timing
```

now becomes part of the behavior.

---

## Another Common Misconception

A common misconception is:

```text
debounce() Changes Values
```

Incorrect.

---

Conceptually:

```text
Input Value
↓
debounce()
↓
Same Value
```

---

The value remains unchanged.

The difference is:

```text
When
The Value Continues
```

---

## Another Common Misconception

A common misconception is:

```text
debounce() Removes Bad Values
```

Incorrect.

That is typically the role of:

```text
filter()
```

---

Instead:

```text
debounce()
```

helps manage:

```text
Rapid Streams Of Valid Values
```

---

## The Key Mental Model

Sometimes:

```text
The Data Is Fine
```

The problem is:

```text
The Frequency Of Updates
```

---

Conceptually:

```text
Value
↓
debounce()
↓
Wait
↓
Continue
```

---

Rather than reacting immediately,

```text
debounce()
```

waits until activity becomes quiet.

---

## Revision Table

| Section | Content |
|----------|----------|
| Type | Flow Timing Control |
| Main Problem | Too many reactions to valid values |
| Solution | debounce() |
| debounce Changes Values? | No |
| debounce Removes Values? | Not the main purpose |
| debounce Controls | Timing |
| Mental Model | Wait For Conversation Pause |
| Common Android Example | Search Box |
| Previous Concept | Filtering Values |
| Next Concept | Combining Flows |

---

## A Natural Question

We now understand:

```text
map()
```

can transform values.

---

```text
filter()
```

can remove values.

---

```text
debounce()
```

can control when values continue.

A natural question appears:

```text
What If The Data I Need Doesn't Come From One Flow?
```

Imagine:

```text
User Information
```

coming from one Flow.

And:

```text
User Settings
```

coming from another Flow.

How can these streams work together?

This question leads us to:

```text
Combining Flows
```

which we will explore next.

---

For this chapter, focus on one idea:

```text
debounce()
```

does not primarily solve:

```text
Bad Data
```

---

It solves:

```text
Too Many Reactions To Valid Data
```
