# Bridging External State Into Compose (`produceState`)

## Observation

Throughout the Atlas,

every piece of state we've displayed in Compose has already existed as Compose state.

For example,

we've used:

- `remember`
- `mutableStateOf`
- `StateFlow`
- `collectAsState()`

In every case,

Compose already knew how to observe the data.

But real applications often need to work with data that doesn't come from Compose at all.

Imagine a battery manager.

```kotlin
batteryManager.setListener { level ->

    ...

}
```

Or a Bluetooth SDK.

```kotlin
bluetoothSdk.onDeviceFound { device ->

    ...

}
```

Or a location provider.

```kotlin
locationProvider.onLocationChanged { location ->

    ...

}
```

Notice something.

None of these expose:

- `State`
- `MutableState`
- `StateFlow`

They simply invoke callbacks.

A natural question appears.

> **How can Compose automatically update the UI when the data source isn't Compose state?**

---

## The First Solution

After everything we've learned so far,

our first instinct is probably this.

```kotlin
@Composable
fun BatteryLevel() {

    var batteryLevel by remember {

        mutableStateOf(0)

    }

    DisposableEffect(Unit) {

        val listener = BatteryListener {

            batteryLevel = it

        }

        batteryManager.register(listener)

        onDispose {

            batteryManager.unregister(listener)

        }

    }

    Text("$batteryLevel%")

}
```

This actually works.

The callback updates Compose state.

Compose recomposes.

The UI stays synchronized.

So...

where's the problem?

---

## The Interesting Part

The answer is:

> **There isn't one.**

This is perfectly valid Compose code.

That's an important observation.

Unlike some of the previous chapters,

the obvious solution isn't wrong.

So why does `produceState` exist?

---

## Looking For A Pattern

Instead of asking,

> **"What's wrong with this code?"**

let's ask a better question.

> **Have we written this pattern before?**

Look closely.

```text
Create Compose State

↓

Start Listening

↓

Update State

↓

Stop Listening
```

That's exactly what we're doing.

Now imagine another feature.

Bluetooth.

Again we write:

```text
Create Compose State

↓

Start Listening

↓

Update State

↓

Stop Listening
```

Now GPS.

Same pattern.

Download progress.

Same pattern.

Sensor data.

Same pattern.

The implementation changes.

The lifecycle doesn't.

---

## An Engineering Observation

Experienced engineers pay attention to repetition.

Whenever the same pattern appears repeatedly,

they ask:

> **Can this be abstracted?**

Notice that every example follows the same structure.

The only thing that changes is:

- where the values come from,
- and what values are produced.

Everything else is identical.

---

## Introducing `produceState`

This is exactly the abstraction `produceState` provides.

Instead of manually writing:

- `remember`
- `mutableStateOf`
- lifecycle management
- state updates

Compose packages those recurring pieces into a single API.

The battery example becomes:

```kotlin
@Composable
fun BatteryLevel() {

    val batteryLevel by produceState(

        initialValue = 0

    ) {

        val listener = BatteryListener {

            value = it

        }

        batteryManager.register(listener)

        awaitDispose {

            batteryManager.unregister(listener)

        }

    }

    Text("$batteryLevel%")

}
```

Notice something interesting.

The code still follows exactly the same lifecycle.

```text
Create State

↓

Start Listening

↓

Update State

↓

Stop Listening
```

Nothing new became possible.

The recurring pattern simply became reusable.

---

## What Changed?

Compare the two approaches.

### Manual Version

```text
remember

↓

mutableStateOf

↓

DisposableEffect

↓

Register Listener

↓

Update State

↓

onDispose
```

---

### `produceState`

```text
produceState

↓

Register Listener

↓

Update value

↓

awaitDispose
```

The capability is identical.

The abstraction is better.

---

## Another Example

Imagine a download SDK.

```kotlin
downloadManager.setProgressListener {

    progress ->

}
```

Without `produceState`,

you'll once again write:

- create state,
- register listener,
- update state,
- unregister listener.

With `produceState`,

the pattern is already built in.

You only describe:

- the initial value,
- where updates come from,
- and how to clean up.

Everything else is handled for you.

---

## Understanding The Name

The name becomes much easier to understand.

```text
External Data

↓

Produce

Compose State

↓

UI
```

You're not producing external data.

You're producing a piece of Compose state whose values come from somewhere outside Compose.

In many ways,

`produceState` acts like a bridge between two worlds.

```text
External World

↓

produceState

↓

Compose Runtime
```

---

## Primitive APIs vs Higher-Level APIs

This chapter teaches something bigger than Compose.

Software libraries often provide two kinds of APIs.

### Primitive APIs

Very flexible.

Require more code.

Examples we've already learned:

- `remember`
- `mutableStateOf`
- `DisposableEffect`
- `LaunchedEffect`

These give us building blocks.

---

### Higher-Level APIs

Recognize a common pattern.

Package it into a reusable abstraction.

`produceState` belongs in this category.

It doesn't add new capabilities.

It simply saves us from rewriting the same lifecycle over and over again.

---

## An Analogy

Imagine building furniture.

Primitive tools are:

- a saw,
- a drill,
- a screwdriver.

You can build almost anything.

But you'll repeat the same sequence of steps many times.

Now imagine someone invents a specialized power tool that performs those common steps together.

It doesn't make new furniture possible.

It simply makes a common pattern easier and less error-prone.

That's exactly what `produceState` does.

---

## Thinking Like A Production Engineer

Whenever you find yourself writing the same lifecycle repeatedly,

ask yourself:

> **Is this really unique logic?**

Or...

> **Am I repeatedly implementing the same pattern?**

Experienced engineers recognize repetition.

Good abstractions emerge from recurring patterns.

`produceState` is one such abstraction.

---

## A Common Misconception

Many developers think:

> **"`produceState` is the only correct way to bridge external data into Compose."**

It isn't.

The manual implementation using:

- `remember`
- `mutableStateOf`
- `DisposableEffect`

is perfectly valid.

`produceState` simply provides a cleaner abstraction for a pattern that appears frequently.

Choosing it is primarily about readability,

maintainability,

and reducing boilerplate.

---

## Bringing Everything Together

Whenever data comes from outside Compose,

think through this decision process.

```text
External Data Source

↓

Need Compose State?

↓

Yes

↓

One-Off Or Highly Custom Logic?

        │

   ┌────┴────┐

   │         │

  Yes       No

   │         │

   ▼         ▼

Build With   Use

Primitives   produceState

(remember +

mutableStateOf +

Effects)
```

Notice something.

This isn't about right versus wrong.

It's about choosing the right level of abstraction.

---

## Revision

### Core Idea

`produceState` bridges external data sources into Compose by producing a piece of Compose state whose values come from outside the Compose Runtime.

---

### Engineering Mindset

Don't ask:

> **"Can I do this without `produceState`?"**

You usually can.

Instead ask:

> **"Am I repeatedly implementing the same lifecycle?"**

If yes,

a higher-level abstraction may make the code simpler and easier to maintain.

---

### Key Insight

`produceState` doesn't introduce new capabilities.

It packages a recurring pattern—

creating state,

observing an external source,

updating state,

and cleaning up—

into a single reusable API.

That's why it exists.

---

## Completing Another Mental Model

Earlier in this part,

we learned:

- **`derivedStateOf`** transforms existing Compose state into other Compose state.

Now we've learned:

- **`produceState`** transforms external data into Compose state.

Notice how both APIs answer different questions about **where state comes from**.

The first derives state from existing state.

The second bridges state from outside Compose.

---

## One Remaining Question

So far,

we've learned how to bring external data **into** Compose.

A natural question appears.

> **What if we want to go in the opposite direction?**

Suppose we already have Compose state,

but another part of our application expects a `Flow`.

How can we safely convert Compose state into a Flow that participates in the coroutine world?

That leads us to the final concept in this part.

**Converting Compose State Into Flow (`snapshotFlow`)**
