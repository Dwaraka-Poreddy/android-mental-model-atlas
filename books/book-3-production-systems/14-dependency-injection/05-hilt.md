# Hilt

## Looking Back

In the previous concepts we learned:

```text
Dependency Injection

↓

Objects Receive Their Dependencies From The Outside
```

And we learned why manual construction fails:

```text
Construction knowledge spreads.
Singletons are managed manually.
Lifecycle is attached to the wrong owner.
Testing requires the real network.
```

We need a framework that solves all four problems.

A natural question appears:

```text
Which Framework Handles This In Android?
```

---

## The Answer

```text
Hilt

↓

Dagger Done For Android.
You Annotate. Hilt Generates The Wiring.
```

Hilt is Google's official DI framework for Android.

It builds on Dagger — a compile-time DI framework —
but removes the manual configuration
that made Dagger difficult to use.

You add four annotations.

Hilt generates all the construction code.

---

## The Four Annotations

```text
@HiltAndroidApp

↓

"Hilt, initialize yourself here.
 This is where you manage the application's dependencies."


@AndroidEntryPoint

↓

"I am an Activity or Fragment.
 Hilt, I need injected things delivered to me."


@Inject constructor

↓

"Hilt, you can build me.
 Here are the things I need."


@HiltViewModel

↓

"I am a ViewModel.
 Hilt, inject my constructor parameters.
 But respect the ViewModel lifecycle."
```

These four annotations wire an entire application.

---

## The Component Hierarchy

Hilt creates objects at the right lifecycle scope automatically.

```text
@HiltAndroidApp (Application)
        ↓
        Owns app-wide singletons
        (UserRepository, AppDatabase, Retrofit)

@AndroidEntryPoint (Activity)
        ↓
        Owns activity-scoped objects

@AndroidEntryPoint (Fragment)
        ↓
        Owns fragment-scoped objects

@HiltViewModel (ViewModel)
        ↓
        Survives configuration changes
        (same instance across rotations)
```

The right object lives at the right level.

The database is not rebuilt on rotation.

The repository is not duplicated across activities.

---

## Minimal Code

The four pieces working together:

```kotlin
// 1. Application — Hilt initializes here
@HiltAndroidApp
class App : Application()

// 2. Activity — Hilt delivers injected objects here
@AndroidEntryPoint
class HomeActivity : AppCompatActivity()

// 3. ViewModel — Hilt provides dependencies, ViewModel manages lifecycle
@HiltViewModel
class HomeViewModel @Inject constructor(
    private val repo: UserRepository
) : ViewModel()

// 4. Repository — Hilt can build this
class UserRepository @Inject constructor(
    private val remote: UserRemoteDataSource,
    private val dao: UserDao
)
```

Hilt reads the annotations.

Hilt generates the construction code at compile time.

Your app never calls `UserRepository(...)` directly.

---

## Production Code

A complete Hilt-wired feature in a food delivery application:

```kotlin
// Application — one class, one annotation
@HiltAndroidApp
class FoodDeliveryApp : Application()

// Activity — one annotation, injection delivered
@AndroidEntryPoint
class HomeActivity : AppCompatActivity() {

    // Hilt delivers the ViewModel automatically
    private val viewModel: HomeViewModel by viewModels()

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_home)
        observeUiState()
    }

    private fun observeUiState() {
        lifecycleScope.launch {
            viewModel.uiState.collect { state ->
                renderState(state)
            }
        }
    }

}

// ViewModel — Hilt provides UserRepository
@HiltViewModel
class HomeViewModel @Inject constructor(
    private val userRepository: UserRepository
) : ViewModel() {

    val uiState: StateFlow<HomeUiState> =
        userRepository.observeCurrentUser()
            .map { user -> HomeUiState.Success(user) }
            .stateIn(viewModelScope, SharingStarted.WhileSubscribed(5_000),
                HomeUiState.Loading)

}

// Repository — Hilt can build this and its dependencies
class UserRepository @Inject constructor(
    private val remote: UserRemoteDataSource,
    private val dao: UserDao
) {
    fun observeCurrentUser(): Flow<User?> =
        dao.observeCurrentUser().map { it?.toDomain() }
}
```

### How To Read It

```text
@HiltAndroidApp on FoodDeliveryApp

↓

Hilt initializes its internal graph here.
All singletons (AppDatabase, Retrofit, UserRepository)
are managed at the Application level.


@AndroidEntryPoint on HomeActivity

↓

Hilt sees this Activity and delivers injected objects.
by viewModels() works because Hilt has registered
a factory for HomeViewModel.


@HiltViewModel on HomeViewModel

↓

Hilt creates HomeViewModel via its generated factory.
The ViewModel survives rotation (standard ViewModel behavior).
Hilt fills the @Inject constructor parameters.


@Inject constructor on UserRepository

↓

Hilt reads: "I can build UserRepository.
I need a UserRemoteDataSource and a UserDao."
Hilt resolves both and passes them in.
```

---

## What Hilt Generates

You do not write this code.

Hilt generates it at compile time:

```text
HomeViewModel_HiltModules
HomeViewModel_Factory
HomeActivity_MembersInjector
FoodDeliveryApp_HiltComponents
... (several more classes)
```

These classes perform the construction you would have
written manually.

Generated at compile time = no runtime reflection = no performance cost.

---

## Production Notes

```text
* Hilt generates Dagger components at compile time.
  There is no runtime cost for using Hilt vs manual construction.

* @HiltAndroidApp must be on the Application class.
  If you forget this, Hilt fails at runtime with a clear error.

* Without @AndroidEntryPoint on an Activity,
  injection into that Activity is silently ignored.
  No error — the injected fields remain null.

* Hilt manages scoping automatically.
  The same UserRepository instance is shared
  across all Activities and ViewModels in a session.
  You do not manage the singleton yourself.

* Hilt is the only officially supported DI framework for Android.
  Koin is an alternative. Production teams at scale use Hilt.
```

---

## Common Misconception

A common misconception is:

```text
"Hilt Is Just Configuration. The Real Work Happens At Runtime."
```

Incorrect.

```text
Hilt generates Kotlin/Java classes at compile time.

↓

At runtime, Hilt's generated code
calls constructors directly.

↓

No reflection.
No runtime class scanning.
No startup cost.
```

Dagger (which Hilt builds on)
was specifically designed to move DI work
from runtime to compile time.

If there is a missing dependency,
the build fails — not the app.

---

## A Natural Question

Hilt wires our own classes together
when they have `@Inject constructor`.

A natural question appears:

```text
How Does Hilt Know How To Build
UserRepository's Dependencies?

How Does @Inject constructor Work?
```

That question leads us to:

```text
@Inject Constructor
```

---

## Revision

### Core Idea

```text
Hilt = Dagger Done For Android.
You Annotate. Hilt Generates The Wiring.
```

### The Four Annotations

```text
@HiltAndroidApp      → Application: Hilt starts here
@AndroidEntryPoint   → Activity/Fragment: Hilt, deliver injected objects here
@Inject constructor  → Any class: Hilt, you can build me
@HiltViewModel       → ViewModel: Hilt + ViewModelProvider lifecycle
```

### Mental Model

```text
@HiltAndroidApp (Application)
        ↓
@AndroidEntryPoint (Activity)
        ↓
@AndroidEntryPoint (Fragment)
        ↓
@HiltViewModel (ViewModel)

Each level scopes objects to the right lifecycle.
```

### Production Recognition

```kotlin
@HiltAndroidApp
class App : Application()

@AndroidEntryPoint
class HomeActivity : AppCompatActivity() {
    private val viewModel: HomeViewModel by viewModels()
}

@HiltViewModel
class HomeViewModel @Inject constructor(
    private val repo: UserRepository
) : ViewModel()
```

### Previous Concept

```text
The Problem With Manual Construction
```

### Next Concept

```text
@Inject Constructor
```
