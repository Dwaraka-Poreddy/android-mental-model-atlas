# Why Performance Matters

## Looking Back

In DOC 5 we learned ViewModels survive rotation.

In DOC 7 we learned Compose recomposes the UI.

In DOC 13 we learned Repositories coordinate data.

We now know how to build a complete production app.

But building it correctly and building it fast are different skills.

```text
Correct App
    ↓
Does what the user expects.
Handles errors. Saves state. Loads data.

Fast App
    ↓
Does all of the above
without making the user wait.
```

These are separate concerns.

A correct app can be painfully slow.

A fast app that is incorrect is useless.

We need both.

---

## The Problem

Poor performance costs you users.

Not gradually. Immediately.

```text
Google Play Store data:

    → 1-star reviews mention "slow" or "laggy"
      more than any other complaint

    → Apps with >200ms startup time
      see a 10% higher uninstall rate

    → 16ms frame budget:
      missing it causes visible jank
```

Users do not wait.

They uninstall.

---

## Three Dimensions

Android developers face three distinct performance problems.

They require different tools and different fixes.

```text
1. Startup — How long from tap to usable UI?

   Cold start (from scratch): should be <500ms
   Warm start (process alive): should be <200ms

   Measured with: Macrobenchmark, Android Vitals


2. Runtime — Does the UI feel smooth at 60fps?

   16ms per frame budget
   Jank = dropped frames = visible stutter

   Measured with: Android Profiler, Layout Inspector


3. Memory — Does the app leak memory or get killed?

   LeakCanary detects leaked references
   OOM (Out Of Memory) = crash

   Measured with: Android Profiler, LeakCanary
```

Each dimension is independent.

You can have great startup performance but terrible memory leaks.

You can scroll smoothly but take 3 seconds to start.

Fixing one does not fix the others.

---

## The Golden Rule

This is the most important principle in all of performance optimization:

```text
Profile First. Fix Second.
```

Never optimize code you haven't profiled.

Here is what happens when you skip profiling:

```text
"This seems like it could be slow"

    ↓

Optimizing the wrong thing

    ↓

Wasted effort
+
Possibly worse performance
+
Code that is harder to read
```

Here is what happens when you profile first:

```text
Profile → Find → Fix → Measure Again
```

This loop is not optional.

It is the only way to know whether a change helped.

---

## The Optimization Loop

```text
Observe slowness
        ↓
Android Profiler / Perfetto
        ↓
   "Which frame is slow?"
   "Which method is taking time?"
        ↓
Fix the specific bottleneck
        ↓
Measure again — did it improve?
        ↓
   If yes: done.
   If no:  return to profiler.
```

Every step is necessary.

Skipping "Measure again" means you don't know if the fix worked.

Skipping the profiler means you don't know what to fix.

---

## Minimal Example

Two ways to mark startup complete — from coarse to precise:

```kotlin
// Precise: reportFullyDrawn() signals when content is actually ready
class HomeActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent { HomeScreen(onReady = { reportFullyDrawn() }) }
    }
}
```

---

## Production Code

A real startup measurement using Macrobenchmark.

Runs on a real device, in a real process, measuring cold start time.

```kotlin
@RunWith(AndroidJUnit4::class)
class StartupBenchmark {

    @get:Rule
    val benchmarkRule = MacrobenchmarkRule()

    @Test
    fun coldStartup() = benchmarkRule.measureRepeated(
        packageName = "com.example.gofood",
        metrics = listOf(StartupTimingMetric()),
        iterations = 5,
        startupMode = StartupMode.COLD
    ) {
        pressHome()
        startActivityAndWait()
    }

    @Test
    fun warmStartup() = benchmarkRule.measureRepeated(
        packageName = "com.example.gofood",
        metrics = listOf(StartupTimingMetric()),
        iterations = 5,
        startupMode = StartupMode.WARM
    ) {
        startActivityAndWait()
    }

}
```

### How To Read It

```text
MacrobenchmarkRule
    ↓
Runs in a separate benchmark module.
Controls a real app process.
Not a unit test — it drives a real device.


StartupMode.COLD
    ↓
Kills the app process before each iteration.
Measures full cold start: from process fork to Activity shown.
This is the worst case.


StartupMode.WARM
    ↓
App process stays alive.
Measures time to recreate Activity from scratch.
Common case after system kills the app.


iterations = 5
    ↓
Runs 5 times and averages.
One measurement is not trustworthy.
Device thermal state and memory pressure vary.


startActivityAndWait()
    ↓
Waits for Activity.onResume().
timeToInitialDisplayMs = time until first frame rendered.
timeToFullDisplayMs = time until reportFullyDrawn() called.
```

---

## The Three Tools Map

```text
Startup problems
    → Macrobenchmark (timeToFullDisplayMs)
    → Android Profiler: CPU tab → Method Trace

Runtime jank
    → Android Profiler: CPU tab → System Trace
    → Layout Inspector: Show Recomposition Counts
    → Compose Compiler Metrics (stability report)

Memory leaks
    → LeakCanary (debug builds, automatic)
    → Android Profiler: Memory tab → Heap Dump
```

Each tool reveals a different layer.

You do not need all of them at once.

Start with the symptom.

Match the symptom to the tool.

---

> **You'll see this in...**
> - **DOC 8 — Inside Compose Runtime**, where stability is the mechanism
>   that determines whether a Composable recomposes or skips
> - **DOC 16 — Testing**, where performance benchmarks become automated
>   tests that catch regressions before they ship

---

## Production Notes

```text
* Profile on real devices, not emulators.
  Emulators have different CPU and GPU profiles.
  Emulator results do not predict real-user experience.

* Profile release builds (or debuggable=false).
  Debug builds run significantly slower.
  R8 optimization and inlining only apply to release.
  Profiling a debug build measures the wrong thing.

* The 16ms frame budget assumes 60fps.
  90fps displays (OnePlus, Pixel) = 11ms budget.
  120fps displays = 8.3ms budget.
  Check your target device refresh rate.

* Premature optimization is the root of all evil (Knuth).
  Measure first. Always.
  Optimizing unmeasured code is guessing.

* Android Vitals in the Play Console shows
  real-user startup data across all installs.
  Use it to confirm lab measurements match production.
```

---

## Common Misconception

A common misconception is:

```text
"Using remember everywhere makes things faster."
```

Incorrect.

```text
remember

    ↓

Adds lambda allocation on each recomposition.
Adds comparison overhead on each recomposition.
Caches the computed value.

Net benefit: only when recomputation is genuinely expensive.
Net cost: always present.
```

`remember` is not free.

Use it when recomputing the value on every recomposition
would cause measurable slowness.

Do not use it defensively everywhere.

Profile first to see whether the computation is actually slow.

---

## A Natural Question

We now know that runtime jank —
dropped frames, visible stutter —
is one of the three performance dimensions.

In Compose, the most common source of runtime jank is:

```text
Unnecessary recompositions.
```

A Composable recomposes when it should have been skipped.

Or recomposes 60 times per second
when its inputs haven't changed.

A natural question appears:

```text
How Do We See Which Composables Are Recomposing
And How Often?
```

That question leads us to:

```text
Compose Recomposition Metrics
```

---

## Revision

### Core Idea

```text
Performance optimization is a three-step process:
measure first, find the bottleneck, fix only what's slow.
```

### The Three Dimensions

```text
Startup    → Cold start should be <500ms
Runtime    → 16ms per frame budget at 60fps
Memory     → Leaks cause OOM crashes
```

### The Loop

```text
Profile → Find → Fix → Measure Again
```

### The Tools

```text
Startup     → Macrobenchmark
Runtime     → Profiler + Layout Inspector + Compiler Metrics
Memory      → LeakCanary + Heap Dump
```

### Previous Concept

```text
DOC 16 — Testing Strategy
```

### Next Concept

```text
Compose Recomposition Metrics
```
