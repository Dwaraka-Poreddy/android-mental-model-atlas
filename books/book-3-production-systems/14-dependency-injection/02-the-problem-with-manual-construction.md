# The Problem With Manual Construction

## Looking Back

In the previous concept we learned:

```text
Dependency Injection

↓

Objects Receive Their Dependencies From The Outside
```

We saw that the alternative — manual construction —
causes three problems:

```text
1. Construction knowledge spreads across every screen.
2. Adding a dependency changes N files.
3. Testing is impossible.
```

We now know DI means objects receive dependencies.

But why is the alternative — manual construction —
so harmful in production?

---

## The Problem Is Not Just Repetition

A developer seeing the construction cascade for the first time
often says:

```text
"I'll just extract it into a helper function."
```

```kotlin
fun buildUserViewModel(context: Context): UserViewModel {
    val db = Room.databaseBuilder(context, ...).build()
    val api = Retrofit.Builder()...create(UserApi::class.java)
    val remote = UserRemoteDataSource(api)
    val repo = UserRepository(remote, db.userDao())
    return UserViewModel(repo)
}
```

This reduces repetition.

But it does not solve the deeper problems.

---

## Problem 1: Construction Knowledge Still Spreads

The helper function still needs to know
the entire dependency tree.

When `UserRepository` gets a third dependency,
the helper function changes.

If there are multiple helper functions
across different modules,
they all change.

The construction knowledge has just moved
from ten Activities
to one or more helper functions.

It is still the wrong place.

```text
Activities
  └── buildUserViewModel(context)  ← construction knowledge here
       └── builds the entire stack

When UserRepository changes:
  → Update buildUserViewModel
  → Find every other builder that does the same thing
  → Update those too
```

---

## Problem 2: Singleton Management Is Manual

`AppDatabase` should be created once.

A Room database built multiple times
from the same file causes data corruption
and performance problems.

Without DI, you manage this manually:

```kotlin
object DatabaseHolder {
    private var instance: AppDatabase? = null

    fun get(context: Context): AppDatabase {
        return instance ?: synchronized(this) {
            instance ?: Room.databaseBuilder(...)
                .build()
                .also { instance = it }
        }
    }
}
```

Now every class that needs the database
references `DatabaseHolder.get(context)`.

`DatabaseHolder` is a global singleton.

You now have:

```text
* Manual thread-safety (synchronized)
* Global mutable state
* Context leaked into every call site
* No clear ownership of the database lifecycle
```

Multiply this pattern across
`RetrofitHolder`, `ApiHolder`, `RemoteDataSourceHolder`...

and your app becomes a collection of global singletons
with tangled lifecycles.

---

## Problem 3: Lifecycle Is Attached To The Wrong Owner

Look at what happens when construction lives in the Activity:

```kotlin
class UserActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        val db = Room.databaseBuilder(this, ...).build()  // built here
        val repo = UserRepository(UserRemoteDataSource(...), db.userDao())
        val viewModel = UserViewModel(repo)
    }

}
```

`onCreate()` runs every time the screen rotates.

The database is rebuilt on every rotation.

The ViewModel is rebuilt on every rotation.

ViewModels exist precisely to survive rotation.

But here, the ViewModel is built inside `onCreate()`
and thrown away on configuration change.

```text
Screen rotates.
→ Activity destroyed.
→ Database connection closed.
→ New Activity created.
→ Database rebuilt.
→ ViewModel rebuilt.
→ Any in-progress work lost.
```

---

## Problem 4: Testing Requires The Real Everything

The hardest problem in practice.

Suppose `UserRepository` calls `UserRemoteDataSource`
to fetch user data.

To test `UserRepository`,
you want to control what `UserRemoteDataSource` returns.

In a well-structured project,
you replace `UserRemoteDataSource` with a fake:

```kotlin
class FakeUserRemoteDataSource : UserRemoteDataSource {
    override suspend fun getCurrentUser(): UserDto =
        UserDto(id = "test-id", name = "Test User")
}
```

But if `UserRepository` builds its own `UserRemoteDataSource`:

```kotlin
class UserRepository {
    private val remote = UserRemoteDataSource(  // built internally
        Retrofit.Builder()...create(UserApi::class.java)
    )
}
```

There is no way to pass the fake.

The only way to test `UserRepository` is with the real network.

```text
BEFORE: ViewModel knows how to construct its dependencies

class HomeViewModel : ViewModel() {
    private val db = Room.databaseBuilder(...)
    private val repo = UserRepository(UserRemoteDataSource(Retrofit...))
}


AFTER: ViewModel declares what it needs

class HomeViewModel @Inject constructor(
    private val userRepository: UserRepository
) : ViewModel()
```

The second `HomeViewModel` can be tested
by passing any `UserRepository` — real or fake.

The first cannot.

---

## The Root Cause

Every problem has the same root:

```text
The object that uses a dependency
also controls how that dependency is created.
```

```text
User of dependency = Creator of dependency

↓

No seam for testing.
No single point of change.
No lifecycle coordination.
No singleton enforcement.
```

Dependency Injection separates these roles:

```text
User of dependency → declares what it needs
Creator of dependency → DI framework

↓

Seam for testing exists (the constructor parameter).
One point of change (the framework's wiring).
Lifecycle coordinated by the framework.
Singleton enforced by the framework.
```

---

## Production Notes

```text
* The database singleton problem causes real production bugs.
  Multiple instances of Room opened against the same file
  can cause SQLiteDatabaseLockedException and data inconsistency.

* The rotation problem is subtle.
  Developers often "fix" it by passing the Application context
  instead of Activity context — but this just hides the symptom.
  The real fix is to not build the database in the Activity at all.

* Manual singletons are a form of global mutable state.
  They make tests order-dependent (one test mutates the singleton,
  the next test sees dirty state).

* The "just use a helper function" approach is a common first step.
  Teams often realize months later that helpers have the same problem.
  The cascade just moved.

* The inability to inject fakes is the most impactful problem
  in practice. Teams without DI spend significant time mocking
  HTTP clients or using real network in tests — both slow and fragile.
```

---

## Common Misconception

A common misconception is:

```text
"I Can Use Companion Objects Or Singletons
 To Manage This Without A DI Framework."
```

```kotlin
companion object {
    private var repo: UserRepository? = null
    fun get(context: Context) = repo ?: UserRepository(...)
}
```

Three problems remain:

```text
1. Memory leaks

   If the singleton holds a Context, it holds the Activity.
   The Activity cannot be garbage-collected.

2. Untestable

   Tests share the same singleton.
   One test's setup leaks into the next test's state.

3. Construction knowledge still spreads

   Every companion object still knows how to build the stack.
   The cascade is distributed, not solved.
```

Singletons manage lifetime.

They do not solve the construction-knowledge problem.

They do not solve the testability problem.

---

## A Natural Question

We have seen exactly why manual construction fails.

Four specific problems.

All solvable.

A natural question appears:

```text
Which Framework Solves These In Android?
And What Does Using It Actually Look Like?
```

That question leads us to:

```text
Hilt
```

---

## Revision

### Core Idea

```text
Manual Construction Fails Because:

  1. Construction knowledge spreads across the codebase.
  2. Singleton management is manual — memory leaks and test pollution.
  3. Lifecycle is wrong — databases rebuilt on rotation.
  4. Testing requires the real network — slow, fragile, unreliable.
```

### Mental Model

```text
BEFORE: ViewModel knows how to build its deps

class HomeViewModel : ViewModel() {
    private val db = Room.databaseBuilder(...)
    private val repo = UserRepository(UserRemoteDataSource(Retrofit...))
}


AFTER: ViewModel declares what it needs

class HomeViewModel @Inject constructor(
    private val userRepository: UserRepository
) : ViewModel()
```

### Production Recognition

```text
If you see this pattern in code review:

  class SomeClass {
      private val dependency = SomeDependency(OtherDependency(...))
  }

Ask: Can this be tested? Can any dependency be replaced with a fake?
If no — it needs DI.
```

### Previous Concept

```text
Why Dependency Injection Exists
```

### Next Concept

```text
Hilt
```
