# @Inject Constructor

## Looking Back

In the previous concept we learned the four Hilt annotations:

```text
@HiltAndroidApp      → Application
@AndroidEntryPoint   → Activity / Fragment
@Inject constructor  → Any class
@HiltViewModel       → ViewModel
```

We saw `@Inject constructor` on `UserRepository`.

We saw that Hilt reads it and generates construction code.

A natural question appears:

```text
How Does Hilt Know HOW To Build UserRepository?

UserRepository Needs UserRemoteDataSource And UserDao.
How Does Hilt Know To Pass Them?
```

---

## The Mental Model

```text
@Inject constructor

↓

"Hilt, when you need a UserRepository,
 build it using these parameters —
 which you also know how to build."
```

`@Inject constructor` is a declaration.

It tells Hilt two things:

```text
1. You are allowed to build me.
2. Here is what I need.
```

Hilt reads the constructor parameters.

Hilt builds each one.

Hilt passes them in.

---

## The Chain

`@Inject` is recursive.

If `UserViewModel` needs `UserRepository`,
and `UserRepository` needs `UserRemoteDataSource`,
Hilt resolves the entire tree:

```text
UserViewModel
  @Inject constructor(repo: UserRepository)
         ↓
  Hilt: "I need a UserRepository."

UserRepository
  @Inject constructor(remote: UserRemoteDataSource, dao: UserDao)
         ↓                           ↓
  Hilt: "I need UserRemoteDataSource and UserDao."

UserRemoteDataSource          UserDao
  @Inject constructor(api)     (from Room — handled differently)
         ↓
  Hilt: "I need UserApi."
```

Hilt resolves the entire tree at compile time.

If any link in the chain is missing,
the build fails — not the app.

---

## Minimal Code

```kotlin
// Hilt can build UserRemoteDataSource
// because its dependency (UserApi) will be provided separately
class UserRemoteDataSource @Inject constructor(
    private val api: UserApi
)

// Hilt can build UserRepository
// because both dependencies have @Inject constructors
class UserRepository @Inject constructor(
    private val remote: UserRemoteDataSource,
    private val dao: UserDao
)
```

Two classes.

Two `@Inject constructor` declarations.

Hilt wires them together.

---

## Production Code

The full chain from DOC 11, DOC 12, and DOC 13 —
now wired by Hilt:

```kotlin
// DOC 11: Remote data source
// UserApi is provided via @Module (not shown here)
class UserRemoteDataSource @Inject constructor(
    private val api: UserApi
) {
    suspend fun getCurrentUser(): UserDto =
        api.getCurrentUser()

    suspend fun updateDisplayName(name: String): UserDto =
        api.updateDisplayName(UpdateNameRequest(name))
}

// DOC 12: DAO comes from Room — Hilt gets it from the @Module
// UserDao itself does not have @Inject constructor

// DOC 13: Repository
class UserRepository @Inject constructor(
    private val remote: UserRemoteDataSource,
    private val dao: UserDao
) {
    fun observeCurrentUser(): Flow<User?> =
        dao.observeCurrentUser()
            .map { entity -> entity?.toDomain() }

    suspend fun refreshCurrentUser() {
        val dto = remote.getCurrentUser()
        dao.insertOrReplace(dto.toEntity())
    }

    suspend fun updateDisplayName(name: String) {
        val dto = remote.updateDisplayName(name)
        dao.insertOrReplace(dto.toEntity())
    }
}

// ViewModel — @HiltViewModel + @Inject constructor
@HiltViewModel
class ProfileViewModel @Inject constructor(
    private val userRepository: UserRepository
) : ViewModel() {

    val user: StateFlow<User?> =
        userRepository.observeCurrentUser()
            .stateIn(viewModelScope, SharingStarted.WhileSubscribed(5_000), null)

    fun refresh() {
        viewModelScope.launch {
            userRepository.refreshCurrentUser()
        }
    }

}
```

### How To Read It

```text
UserRemoteDataSource @Inject constructor(api: UserApi)

↓

Hilt sees: "I can build UserRemoteDataSource.
I need a UserApi — which is provided via @Module."


UserRepository @Inject constructor(remote, dao)

↓

Hilt sees: "I can build UserRepository.
I need UserRemoteDataSource — I build that (see above).
I need UserDao — provided via the @Module for Room."


ProfileViewModel @Inject constructor(userRepository)

↓

Hilt sees: "I can build ProfileViewModel.
I need UserRepository — I build that (see above)."

@HiltViewModel ensures the ViewModelProvider
manages its lifecycle. Hilt fills the constructor.
```

---

## Constructor Injection vs Field Injection

There are two places `@Inject` can appear:

```text
@Inject constructor   → Constructor injection
                        Used on any class Hilt manages.

@Inject (on a field)  → Field injection
                        Used ONLY inside @AndroidEntryPoint classes
                        (Activities, Fragments, Services).
```

```kotlin
// Constructor injection — preferred
class UserRepository @Inject constructor(
    private val remote: UserRemoteDataSource
)

// Field injection — only when you cannot control the constructor
@AndroidEntryPoint
class HomeActivity : AppCompatActivity() {

    @Inject lateinit var analyticsTracker: AnalyticsTracker

}
```

Field injection is for classes
where you cannot control the constructor —
like Activities, where Android creates the instance.

Constructor injection is for everything else.

---

## Production Notes

```text
* @Inject on the constructor means Hilt can provide this class.
  Without it, Hilt has no knowledge of how to build it.

* Prefer constructor injection over field injection.
  Constructor parameters are explicit.
  Field injection parameters are invisible until runtime.

* If a class has multiple constructors, only one can be @Inject.
  Hilt does not know which one to use otherwise — it fails at build.

* @Inject does not create a singleton by default.
  A new instance is created each time one is needed.
  To make a class a singleton, add @Singleton to the class.

* Not all classes can use @Inject constructor.
  Third-party classes (Retrofit, OkHttp, Room database, UserApi)
  cannot have their source modified.
  These require @Module and @Provides — the next concept.
```

---

## Common Misconception

A common misconception is:

```text
"@Inject Works On Everything."
```

Incorrect.

```kotlin
// This works — you own this class
class UserRepository @Inject constructor(...)

// This does NOT work — you cannot modify Retrofit's source
class Retrofit @Inject constructor(...)  // compile error: not your class
```

```text
@Inject constructor

↓

Works on classes you own and can annotate.

---

Third-party classes (Retrofit, OkHttp, Room database)

↓

Need @Module + @Provides
(You write a function that builds the object.
 Hilt calls your function.)
```

`@Inject` requires access to the source.

For anything you cannot modify,
you describe how to build it in a `@Module`.

---

## A Natural Question

`@Inject constructor` wires together our own classes.

But `UserRepository` needs `UserDao`.

`UserDao` comes from Room — we cannot add `@Inject` to it.

`UserRemoteDataSource` needs `UserApi`.

`UserApi` comes from Retrofit — we cannot add `@Inject` to it.

A natural question appears:

```text
Retrofit, OkHttp, And Room Cannot Have @Inject.
How Do We Tell Hilt How To Build Them?
```

That question leads us to:

```text
@Module And @Provides
```

---

## Revision

### Core Idea

```text
@Inject constructor

↓

"Hilt, when you need me,
 build me using these parameters —
 which you also know how to build."
```

### The Chain

```text
UserViewModel
  @Inject constructor(repo: UserRepository)
        ↓
UserRepository
  @Inject constructor(remote: UserRemoteDataSource, dao: UserDao)
        ↓                              ↓
UserRemoteDataSource              UserDao
  @Inject constructor(api)         (from @Module)
```

Hilt resolves the entire tree at compile time.

### Production Recognition

```kotlin
class UserRepository @Inject constructor(
    private val remote: UserRemoteDataSource,
    private val dao: UserDao
)
```

```text
@Inject on constructor = "Hilt, you can build me."
Constructor parameters = "Here is what I need."
```

### Previous Concept

```text
Hilt
```

### Next Concept

```text
@HiltViewModel
```
