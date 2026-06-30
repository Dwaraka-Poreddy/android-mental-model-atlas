# Memory Leaks

## Looking Back

In DOC 2 we learned about `viewModelScope` —
coroutines that cancel automatically when the ViewModel clears.

In DOC 5 we learned that ViewModels survive rotation
but are cleared when the user leaves permanently.

In DOC 7 we learned about Composables
and how `DisposableEffect` handles lifecycle-aware cleanup.

Startup is fast.

Recompositions are minimal.

The UI scrolls at 60fps.

One more threat remains.

```text
Memory leaks.
```

---

## The Problem

Here is what a memory leak looks like in practice:

```text
User navigates to HomeScreen
        ↓
HomeFragment created — takes memory
        ↓
User navigates away
        ↓
HomeFragment should be garbage collected
        ↓
But: a background listener still holds a reference to HomeFragment
        ↓
HomeFragment can't be collected
        ↓
User navigates to HomeScreen again
        ↓
New HomeFragment created — old one still alive
        ↓
Every navigation adds memory
        ↓
Memory fills up → App killed by OS (OutOfMemoryError)
```

The app doesn't crash at the leak site.

It crashes later, seemingly unrelated,
when the OS can no longer allocate memory.

This makes memory leaks hard to diagnose without tooling.

---

## The Mental Model

```text
Memory Leak
    ↓
An object that should be garbage collected
but cannot be,
because something else still holds a reference to it.
```

Garbage collection works by finding objects
with no live references and reclaiming their memory.

If any live reference points to your object,
the garbage collector cannot touch it.

```text
HomeFragment (should be dead)
    ↑
MyClickListener (anonymous inner class)
    ↑
BackgroundTimer (static field — always alive)

↓

BackgroundTimer is alive.
BackgroundTimer holds MyClickListener.
MyClickListener holds HomeFragment.
HomeFragment cannot be collected.
```

One live reference is enough to prevent collection.

---

## Common Leak Sources

```text
1. Static references holding Context or View
   Static fields live for the entire app process.
   Any object they hold is never collected.

2. Inner classes (anonymous Runnable, Listener)
   Anonymous inner classes hold a reference to their outer class.
   An anonymous Runnable inside a Fragment
   keeps the Fragment alive as long as the Runnable runs.

3. ViewBinding held past Fragment.onDestroyView()
   Fragment views are destroyed before the Fragment itself.
   Holding binding past onDestroyView prevents view collection.

4. Callbacks registered but never unregistered
   Registering a listener on a singleton or long-lived object
   without unregistering = the singleton holds your Fragment forever.

5. Coroutines launched in GlobalScope
   GlobalScope coroutines outlive their caller.
   A GlobalScope coroutine launched in a Fragment
   holds a reference to the Fragment until the coroutine completes.
```

---

## LeakCanary

LeakCanary is the standard tool for detecting memory leaks in Android.

```kotlin
// build.gradle.kts
dependencies {
    debugImplementation("com.squareup.leakcanary:leakcanary-android:2.14")
}
```

That is the entire setup.

LeakCanary installs itself automatically in debug builds.

When a leak happens:

```text
LeakCanary detects that a Fragment or Activity
was not collected after it should have been
        ↓
Triggers a heap dump
        ↓
Analyzes the reference chain
        ↓
Shows a notification: "1 retained object"
        ↓
Tap → see the leak trace:

HomeFragment
  ↓ (held by)
MyClickListener (anonymous inner class)
  ↓ (held by)
BackgroundTimer (static field)
```

The leak trace shows the exact chain of references
preventing collection.

Fix the chain at any point and the leak disappears.

---

## Production Code

The two most common leak patterns in modern Android
and how to fix them.

```kotlin
// ——— Pattern 1: DisposableEffect without cleanup ———

// LEAKS: listener registered but never unregistered
@Composable
fun LocationTracker() {
    val context = LocalContext.current

    DisposableEffect(Unit) {
        val locationManager =
            context.getSystemService(LocationManager::class.java)
        val listener = LocationListener { location ->
            // This lambda captures `context`
            // which captures the composition
        }
        locationManager.requestLocationUpdates(
            "gps", 0L, 0f, listener
        )

        onDispose {
            // ← THIS is the fix: always clean up in onDispose
            locationManager.removeUpdates(listener)
        }
    }
}
```

```kotlin
// ——— Pattern 2: ViewBinding held past onDestroyView ———

// LEAKS: _binding not nullified — holds View reference
class HomeFragment : Fragment(R.layout.fragment_home) {

    private var _binding: FragmentHomeBinding? = null
    private val binding get() = _binding!!  // ← use this accessor form

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        _binding = FragmentHomeBinding.bind(view)
    }

    override fun onDestroyView() {
        super.onDestroyView()
        _binding = null   // ← nullify here, not in onDestroy
    }

}
```

### How To Read It

```text
DisposableEffect → onDispose
    ↓
Compose's lifecycle-aware cleanup mechanism.
onDispose runs when the Composable leaves the composition.

Register in the DisposableEffect body.
Unregister in onDispose.
Always. No exceptions.

Forgetting onDispose = the listener lives forever
= the composition is held forever
= memory leak.


_binding = null in onDestroyView
    ↓
The Fragment's view is destroyed in onDestroyView.
The Fragment itself is destroyed later in onDestroy.

If _binding is not null in onDestroyView,
the binding holds a reference to the (now destroyed) views.
The views cannot be collected.

The two-field pattern (_binding nullable, binding non-null accessor)
is the standard Android approach to this.
```

---

## The Coroutine Leak

Coroutines are the most common leak in modern Android.

```kotlin
// LEAKS: coroutine outlives the Fragment
class HomeFragment : Fragment() {
    fun loadData() {
        GlobalScope.launch {             // ← lives forever
            val data = repository.fetch()
            updateUI(data)              // ← Fragment may be gone
        }
    }
}

// SAFE: coroutine is cancelled when the Fragment's view is destroyed
class HomeFragment : Fragment() {
    fun loadData() {
        viewLifecycleOwner.lifecycleScope.launch {  // ← tied to view lifecycle
            val data = repository.fetch()
            updateUI(data)
        }
    }
}

// SAFEST (DOC 2): use viewModelScope in the ViewModel
class HomeViewModel @HiltViewModel @Inject constructor(
    private val repository: HomeRepository
) : ViewModel() {
    fun loadData() {
        viewModelScope.launch {         // ← cancelled when ViewModel clears
            val data = repository.fetch()
            _uiState.value = UiState.Success(data)
        }
    }
}
```

`viewModelScope` is the standard.

It cancels automatically when the ViewModel is cleared.

No manual cancellation. No coroutine leaks.

---

> **You'll see this in...**
> - **DOC 16 — Testing**, where memory tests with LeakCanary integrate
>   into your test suite via automated navigation flows
> - **DOC 18 — Android Framework Deep Dive**, where ART's garbage collection
>   and object lifetime are explained at the platform level

---

## Production Notes

```text
* LeakCanary is the standard tool — every Android team uses it.
  Add it to debug builds and leave it there.
  It is not optional for production apps.

* Most leaks in modern Android (ViewModel + Compose) come from:
  GlobalScope coroutines, unregistered listeners, static references.
  ViewBinding leaks are common in Fragment-based apps.

* viewModelScope (DOC 2) prevents coroutine leaks by design.
  It cancels automatically when the ViewModel clears.
  Prefer viewModelScope over lifecycleScope for any async work
  that should outlive the Fragment's view.

* Compose largely eliminates activity/fragment lifecycle leaks.
  DisposableEffect handles registration and cleanup.
  The pattern makes the cleanup explicit and co-located
  with the registration.

* Run LeakCanary in CI with automated navigation tests.
  Navigate to every screen, then back.
  LeakCanary will report retained objects.
  This catches leaks before they reach production.
```

---

## Common Misconception

A common misconception is:

```text
"Compose eliminates all memory leaks."
```

Incorrect.

```text
Compose eliminates many View-era leak patterns:

    ✓ No more anonymous inner class listeners on views
    ✓ DisposableEffect makes cleanup explicit
    ✓ No more findViewById holding stale references

Compose does NOT eliminate:

    ✗ Coroutine leaks (GlobalScope, manually managed Jobs)
    ✗ Context leaks via static fields
    ✗ DisposableEffect missing onDispose
    ✗ ViewModel held by a static singleton
    ✗ Callbacks registered on long-lived objects
```

Compose reduces the surface area for leaks.

It does not eliminate the category.

---

## A Natural Question

We have now covered all three performance dimensions:

```text
Runtime jank     → Compose Recomposition Metrics
Startup speed    → Baseline Profiles
Memory leaks     → LeakCanary
```

A natural question appears:

```text
How Do We Know When We Have Fixed Enough?
How Do We Prevent Performance Regressions
From Re-Appearing After We Ship?
```

The answer is:

```text
Performance benchmarks in CI.
```

Which connects directly back to:

```text
DOC 16 — Testing Strategy
```

---

## Revision

### Core Idea

```text
Memory Leak
    ↓
An object that should be garbage collected
but cannot be,
because something else holds a reference to it.
```

### LeakCanary

```text
debugImplementation("com.squareup.leakcanary:leakcanary-android:2.14")
    ↓
Installs automatically in debug builds.
Detects retained objects after lifecycle events.
Shows the full reference chain causing the leak.
```

### The Fix Pattern

```text
Register something?
    ↓
Unregister it when done.
    ↓
DisposableEffect → onDispose
lifecycleScope → cancelled with lifecycle
viewModelScope → cancelled when ViewModel clears
```

### Common Leaks

```text
GlobalScope coroutines       → use viewModelScope
Anonymous inner class        → use lambda or static class
ViewBinding past onDestroyView → nullify in onDestroyView
Listeners never unregistered → DisposableEffect onDispose
Static Context reference     → avoid; use ApplicationContext
```

### Previous Concept

```text
Baseline Profiles
```

### Next Concept

```text
DOC 18 — Android Framework Deep Dive
```
