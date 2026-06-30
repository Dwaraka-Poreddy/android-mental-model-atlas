# @HiltViewModel

## Looking Back

In the previous concept we learned:

```text
@Inject constructor

↓

"Hilt, you can build me.
 Here is what I need."
```

We saw the full chain from `UserViewModel`
down through `UserRepository`, `UserRemoteDataSource`, and `UserDao`.

We know `@Inject constructor` wires our classes together.

But ViewModels are special.

A natural question appears:

```text
ViewModels Are Not Created Directly.
They Are Created By The ViewModelProvider System (DOC 5).
How Does Hilt Handle This?
```

---

## The Problem

In DOC 5, we learned that ViewModels survive rotation:

```text
Screen rotates.

↓

Activity is destroyed.
ViewModel survives.
New Activity receives the same ViewModel instance.
```

This happens because Android does not create ViewModels directly.

It uses `ViewModelProvider`:

```kotlin
// Android does this for you (simplified)
val viewModel = ViewModelProvider(owner, factory)[HomeViewModel::class.java]
```

The `factory` knows how to create the ViewModel.

If you add `@Inject constructor` to a ViewModel
without telling Hilt about the ViewModelProvider system,
Hilt would create a new ViewModel directly —
bypassing the lifecycle management.

```text
@Inject alone on a ViewModel

↓

Hilt creates the instance directly.
ViewModelProvider doesn't know about it.
The ViewModel does NOT survive rotation.
```

---

## The Fix

```text
@HiltViewModel

↓

"I am a ViewModel.
 Use ViewModelProvider for my lifecycle.
 Use Hilt to inject my constructor parameters."
```

`@HiltViewModel` makes Hilt generate a `ViewModelProvider.Factory`
for your ViewModel automatically.

You write the ViewModel.

Hilt generates the factory.

Android uses the factory.

The ViewModel survives rotation.

---

## What @HiltViewModel Eliminates

Without `@HiltViewModel`,
every ViewModel with dependencies needs a factory:

```kotlin
// Without @HiltViewModel — you write this for every ViewModel
class HomeViewModelFactory @Inject constructor(
    private val repo: UserRepository
) : ViewModelProvider.Factory {

    override fun <T : ViewModel> create(modelClass: Class<T>): T {
        @Suppress("UNCHECKED_CAST")
        return HomeViewModel(repo) as T
    }

}
```

And then in the Activity:

```kotlin
// Without @HiltViewModel — you also wire the factory in the Activity
@AndroidEntryPoint
class HomeActivity : AppCompatActivity() {

    @Inject lateinit var factory: HomeViewModelFactory

    private val viewModel: HomeViewModel by lazy {
        ViewModelProvider(this, factory)[HomeViewModel::class.java]
    }

}
```

`@HiltViewModel` eliminates both of these.

```kotlin
// With @HiltViewModel — this is all you write
@HiltViewModel
class HomeViewModel @Inject constructor(
    private val repo: UserRepository
) : ViewModel()

// And in the Activity — by viewModels() just works
@AndroidEntryPoint
class HomeActivity : AppCompatActivity() {
    private val viewModel: HomeViewModel by viewModels()
}
```

Hilt generates the factory.

`by viewModels()` finds and uses it.

---

## Production Code

A complete `HomeViewModel` from a food delivery application —
`@HiltViewModel`, `@Inject constructor`, and a real Flow pipeline:

```kotlin
@HiltViewModel
class HomeViewModel @Inject constructor(
    private val userRepository: UserRepository
) : ViewModel() {

    // Public UI state — observed by the composable or Fragment
    val uiState: StateFlow<HomeUiState> = userRepository
        .observeCurrentUser()
        .map { user ->
            if (user != null) HomeUiState.Success(user)
            else HomeUiState.Empty
        }
        .catch { emit(HomeUiState.Error(it.message ?: "Unknown error")) }
        .stateIn(
            scope = viewModelScope,
            started = SharingStarted.WhileSubscribed(5_000),
            initialValue = HomeUiState.Loading
        )

    // Called when the user pulls to refresh
    fun refresh() {
        viewModelScope.launch {
            userRepository.refreshCurrentUser()
        }
    }

    // Called when the user updates their display name
    fun updateDisplayName(name: String) {
        viewModelScope.launch {
            userRepository.updateDisplayName(name)
        }
    }

}

sealed interface HomeUiState {
    data object Loading : HomeUiState
    data object Empty : HomeUiState
    data class Success(val user: User) : HomeUiState
    data class Error(val message: String) : HomeUiState
}
```

### How To Read It

```text
@HiltViewModel

↓

Hilt generates HomeViewModel_Factory.
ViewModelProvider uses this factory to create the ViewModel.
The ViewModel survives configuration changes.


@Inject constructor(private val userRepository: UserRepository)

↓

Hilt resolves UserRepository using @Inject constructor.
Hilt passes it in when creating the ViewModel.
You do not call UserRepository() anywhere.


uiState: StateFlow<HomeUiState>

↓

Transforms the Flow from UserRepository (DOC 4).
stateIn converts it to a StateFlow (DOC 4).
WhileSubscribed(5_000): stops collecting 5 seconds
after the last subscriber disappears.
Survives rotation because the ViewModel survives rotation.


refresh() and updateDisplayName()

↓

Actions triggered by the UI.
viewModelScope is cancelled when the ViewModel is cleared.
No coroutine leaks.
```

---

## The Rotation Story

```text
User rotates device.

↓

HomeActivity is destroyed.
HomeViewModel.onCleared() is NOT called.
(The ViewModel is not dead — it is retained.)

↓

New HomeActivity is created.
by viewModels() asks ViewModelProvider for HomeViewModel.
ViewModelProvider finds the retained instance.
Returns it.

↓

uiState is already collecting.
No restart. No loading flash. No lost state.
```

This is why `@HiltViewModel` matters.

Without it, the ViewModel is not retained.

The user sees a loading spinner on every rotation.

---

> **You'll see this in...**
> - **DOC 15 — Background Work**, where Hilt injects dependencies into Workers running outside the Activity lifecycle
> - **DOC 16 — Testing**, where tests replace the injected UserRepository with a fake, making ViewModel tests fast and reliable without a network or database

---

## Production Notes

```text
* @HiltViewModel must be used together with @Inject constructor.
  One without the other does not produce a working ViewModel.

* by viewModels() in an @AndroidEntryPoint Activity
  automatically uses the Hilt-generated factory.
  No extra setup required.

* by activityViewModels() also works with Hilt.
  The ViewModel is scoped to the Activity and shared
  across all Fragments in that Activity.

* SavedStateHandle can be injected as a constructor parameter
  alongside your own dependencies.
  Hilt handles it automatically.

* @HiltViewModel does not affect testability negatively.
  In tests, you construct the ViewModel directly
  with a fake repository — no factory needed in tests.
```

---

## Common Misconception

A common misconception is:

```text
"@HiltViewModel Just Adds @Inject To The ViewModel.
 The Two Annotations Are Interchangeable."
```

Incorrect.

```text
@Inject constructor alone on a ViewModel

↓

Hilt can build the ViewModel.
But it bypasses ViewModelProvider.
The ViewModel does NOT survive rotation.


@HiltViewModel + @Inject constructor

↓

Hilt generates a ViewModelProvider.Factory.
Android uses the factory through ViewModelProvider.
The ViewModel survives rotation correctly.
```

`@HiltViewModel` and `@Inject constructor` together
are required for correct behavior.

Neither works alone for ViewModels.

---

## A Natural Question

All our wiring so far assumes
we can add `@Inject constructor`
to every class in the chain.

But `UserApi` comes from Retrofit.

`UserDao` comes from Room.

`OkHttpClient` comes from a third-party library.

None of these have `@Inject constructor`.

A natural question appears:

```text
How Does Hilt Build Objects
It Cannot Add @Inject To?
```

That question leads us to:

```text
@Module And @Provides
```

---

## Revision

### Core Idea

```text
@HiltViewModel

↓

"I am a ViewModel.
 Use ViewModelProvider for my lifecycle.
 Use Hilt to inject my constructor parameters."
```

### The Full Wiring

```text
Button tap (Compose — DOC 7)
        ↓
HomeViewModel @HiltViewModel     ← DOC 5 lifecycle + DOC 14 injection
        ↓
UserRepository @Inject           ← DOC 13 architecture
        ↓
UserRemoteDataSource @Inject     ← DOC 11 networking
UserDao from Room                ← DOC 12 persistence
        ↑
Hilt wires all of this           ← DOC 14
```

### Production Recognition

```kotlin
@HiltViewModel
class HomeViewModel @Inject constructor(
    private val userRepository: UserRepository
) : ViewModel() {

    val uiState: StateFlow<HomeUiState> = userRepository
        .observeCurrentUser()
        .map { HomeUiState.Success(it) }
        .stateIn(viewModelScope, SharingStarted.WhileSubscribed(5_000),
            HomeUiState.Loading)

}
```

```text
@HiltViewModel = lifecycle management by ViewModelProvider
@Inject constructor = dependency resolution by Hilt
StateFlow = UI state stream that survives rotation
```

### Previous Concept

```text
@Inject Constructor
```

### Next Concept

```text
@Module And @Provides
```
