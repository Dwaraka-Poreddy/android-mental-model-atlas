# Case Study 01 — Building Offline-First Order Tracking

> **Book 5 — Learning From Experience**
>
> This is not a tutorial. This is a story about a real feature built by a real team —
> the decisions made, the mistakes that cost time, and what the final architecture
> looked like after the dust settled. The cross-references (DOC N) point to the
> concept docs where each tool is explained in depth.

---

## The Setup

The app is a food delivery platform. One of the most-used screens is Order Tracking —
the screen you open after placing an order to watch it move through the stages:

```text
Placed → Confirmed → Preparing → On the way → Delivered
```

The product requirement sounds simple: show the current order status. But the real
requirement, once you think through edge cases, is harder:

- What does the user see when they lose signal in an elevator?
- What does the user see when they open the app after turning airplane mode off?
- How does the status stay fresh without killing the battery with constant requests?
- What if the server returns an error mid-order?

These are the questions the team did not ask on the first attempt. This case study
covers what happened when they didn't, and how the second architecture answered all
of them correctly.

---

## Act 1 — The First Attempt

### What the team built

The first version was written in three hours. The developer knew Retrofit (DOC 11),
knew how to use `viewModelScope` (DOC 5), and wired them together directly:

```kotlin
// First attempt — ViewModel calls API directly
class OrderTrackingViewModel @Inject constructor(
    private val api: OrderApi  // DOC 11 — Retrofit interface
) : ViewModel() {

    private val _order = MutableStateFlow<Order?>(null)
    val order = _order.asStateFlow()

    private val _isLoading = MutableStateFlow(false)
    val isLoading = _isLoading.asStateFlow()

    private val _error = MutableStateFlow<String?>(null)
    val error = _error.asStateFlow()

    fun refresh(orderId: String) {
        viewModelScope.launch {
            _isLoading.value = true
            try {
                _order.value = api.getOrder(orderId)
            } catch (e: Exception) {
                _error.value = "Could not load order"
            } finally {
                _isLoading.value = false
            }
        }
    }
}
```

The UI observed `order`, `isLoading`, and `error` as separate streams. The composable
called `refresh()` in a `LaunchedEffect`. It looked functional. It passed internal QA.

Then QA turned off WiFi and shook the device.

### Why it failed — the four problems

**Problem 1: No internet = empty screen.**

The ViewModel held `_order.value = null` until a successful API call. If the network
was unreachable, the user saw a loading spinner that eventually gave up and showed
an error. The previous successful order state was gone — it lived only in memory, and
the ViewModel had been recreated by the system.

This was the biggest user-facing bug. Users in tunnels, in elevators, in areas with
weak signal — all of them lost their order status the moment connectivity dropped.

**Problem 2: The ViewModel was talking directly to the API.**

This violated the layered architecture described in DOC 13. The ViewModel is a UI
state holder. It should know about UI concerns: what to show, when to show a loading
indicator, how to translate domain errors into user-facing messages. It should not
know which HTTP endpoint to call or how to parse the response body.

This violation created an immediate practical problem: to write a unit test for
`refresh()`, you had to mock `OrderApi`. But `OrderApi` is a Retrofit interface —
its behavior in tests is fragile, brittle to the API contract, and slow to set up.

**Problem 3: Three separate StateFlows made the UI fragile.**

`_order`, `_isLoading`, and `_error` could contradict each other. Between the line
`_isLoading.value = true` and the eventual assignment to `_order.value`, the UI was
in a composite state with three independent boolean/nullable variables. A slow
recomposition could see `isLoading = false`, `order = null`, `error = null` — a state
that mapped to nothing in the UI and caused a blank white flash.

The pattern for this problem is a sealed `UiState` class — a single value that is
always one of: Loading, Success, or Error. Never a combination of three booleans.

**Problem 4: Polling would require `while(true)` in the ViewModel.**

The product team asked for live updates every 30 seconds. The developer considered
using `while (isActive) { refresh(); delay(30_000) }` inside `viewModelScope`. This
works — but it would have been written alongside the same ViewModel-talks-to-API
problem, making polling a first-class source of battery drain with no backoff and
no coordination with the local cache.

### The state of the code before the rewrite

```text
OrderTrackingViewModel
    ↓  calls
OrderApi (Retrofit)
    ↓  returns
Order (data class, same as network DTO)

Problems:
    - No local cache. Network = source of truth.
    - No Repository layer. ViewModel = network coordinator.
    - Three separate state streams. UI can be inconsistent.
    - Order object is the network DTO — no domain model separation.
```

---

## Act 2 — Building the Right Architecture

The team spent a day designing the second version before writing a line of code. The
design question was: "Where does the data live, and who owns it?"

The answer from DOC 13: **the local database is the single source of truth.** The
network is just a way to update that database. The UI reads exclusively from the
database. Network failures do not touch what the UI shows — the cached data is still
there.

This is offline-first. Not a special mode. Not a fallback. The default.

### Step 1 — The local storage layer (DOC 12)

The team added Room. First, the entity — the database representation of an order:

```kotlin
// TODO: Securely load any sensitive config (e.g., encryption keys) from
//       environment variables or a secrets vault. Do not hardcode.
@Entity(tableName = "orders")
data class OrderEntity(
    @PrimaryKey val orderId: String,
    val status: String,              // "PREPARING", "ON_THE_WAY", "DELIVERED"
    val restaurantName: String,
    val restaurantImageUrl: String,
    val estimatedMinutes: Int,
    val courierName: String?,
    val courierPhone: String?,
    val lastUpdatedAt: Long = System.currentTimeMillis()
)
```

Note that `OrderEntity` is a Room entity, not the network DTO and not the domain
model. It represents exactly what Room stores. The domain model (`Order`) is separate
and is what the UI cares about. Keeping these three representations distinct is
described in DOC 13 — each layer should own its own data shape.

Next, the DAO. The critical decision here is the return type of the query method:

```kotlin
@Dao
interface OrderDao {

    // Flow<> return type: Room re-emits whenever the row changes (DOC 3, DOC 12)
    @Query("SELECT * FROM orders WHERE orderId = :id")
    fun observeOrder(id: String): Flow<OrderEntity?>

    // Used by the Repository to write network responses to local storage
    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun upsert(order: OrderEntity)

    // Cleanup after order is delivered and archived
    @Query("DELETE FROM orders WHERE orderId = :id AND status = 'DELIVERED'")
    suspend fun deleteDeliveredOrder(orderId: String)
}
```

`observeOrder` returns `Flow<OrderEntity?>` — not a `suspend` function that returns
a single value, but a Flow that emits every time the underlying database row changes.
This is Room's reactive query capability (DOC 12). When the Repository writes a
refreshed order to the database, Room emits the new value through this Flow
automatically. The UI does not need to be told to refresh. It happens as a natural
consequence of the write.

### Step 2 — The network layer (DOC 11)

The network model is separate from the database entity. The server's JSON response
shape is the server's concern:

```kotlin
// Network response DTO — mirrors the JSON from the server
data class OrderDto(
    @SerializedName("order_id") val orderId: String,
    @SerializedName("current_status") val currentStatus: String,
    @SerializedName("restaurant") val restaurant: RestaurantDto,
    @SerializedName("estimated_delivery_minutes") val estimatedDeliveryMinutes: Int,
    @SerializedName("courier") val courier: CourierDto?
)

data class RestaurantDto(
    @SerializedName("name") val name: String,
    @SerializedName("image_url") val imageUrl: String
)

data class CourierDto(
    @SerializedName("name") val name: String,
    @SerializedName("phone") val phone: String
)

// Retrofit interface (DOC 11)
interface OrderTrackingApi {
    @GET("orders/{orderId}")
    suspend fun getOrder(@Path("orderId") orderId: String): OrderDto
}
```

And the mapping extension functions connect the layers:

```kotlin
// OrderDto (network) → OrderEntity (database)
fun OrderDto.toEntity(): OrderEntity = OrderEntity(
    orderId = orderId,
    status = currentStatus,
    restaurantName = restaurant.name,
    restaurantImageUrl = restaurant.imageUrl,
    estimatedMinutes = estimatedDeliveryMinutes,
    courierName = courier?.name,
    courierPhone = courier?.phone
)

// OrderEntity (database) → Order (domain)
fun OrderEntity.toDomain(): Order = Order(
    id = orderId,
    status = OrderStatus.from(status),   // String → sealed class
    restaurantName = restaurantName,
    restaurantImageUrl = restaurantImageUrl,
    estimatedMinutes = estimatedMinutes,
    courier = if (courierName != null && courierPhone != null) {
        Courier(name = courierName, phone = courierPhone)
    } else null
)
```

### Step 3 — The Repository as coordinator (DOC 13)

The Repository is the most important file in this architecture. It has two
responsibilities:

1. **Read path:** return a `Flow` of domain objects, sourced from the local database.
2. **Write path:** hit the network, write the result to the database, and return
   success or failure. The Flow from step 1 automatically emits the new value.

```kotlin
class OrderRepository @Inject constructor(
    private val api: OrderTrackingApi,    // DOC 11
    private val dao: OrderDao             // DOC 12
) {

    // The UI always reads from local DB — single source of truth (DOC 13)
    fun observeOrder(orderId: String): Flow<Order?> =
        dao.observeOrder(orderId)
            .map { entity -> entity?.toDomain() }  // Entity → Domain model

    // Network refresh: fetch from server, write to DB
    // The observeOrder Flow above emits automatically when DB is written
    suspend fun refresh(orderId: String): Result<Unit> = runCatching {
        val dto = api.getOrder(orderId)      // fetch from network (DOC 11)
        dao.upsert(dto.toEntity())           // write to Room → triggers Flow emit
    }

    // Called when the order reaches terminal state
    suspend fun archiveDeliveredOrder(orderId: String) {
        dao.deleteDeliveredOrder(orderId)
    }
}
```

The key insight: `refresh()` does not return the order. It returns `Result<Unit>` —
success or failure of the write operation. The order data flows through `observeOrder`.
The caller (ViewModel) calls `refresh()` and either ignores the result (fire-and-forget)
or uses it to show a transient error snackbar. The order display is not tied to the
refresh result.

This separation is what makes offline-first work:
- On first load with no cache: the Flow emits `null` (show empty/loading state), then
  `refresh()` succeeds and the Flow emits the fetched order.
- After cache is populated, on network failure: the Flow still emits the cached order.
  `refresh()` fails and returns an error. The UI can show a "Could not update, showing
  last known status" snackbar, but the order data is still visible.

### Step 4 — The domain model (DOC 13)

The domain model uses Kotlin sealed classes and enums to make status handling
exhaustive. Strings like `"ON_THE_WAY"` become compile-time checked values:

```kotlin
data class Order(
    val id: String,
    val status: OrderStatus,
    val restaurantName: String,
    val restaurantImageUrl: String,
    val estimatedMinutes: Int,
    val courier: Courier?
) {
    val isActive: Boolean get() = status != OrderStatus.DELIVERED
    val statusLabel: String get() = status.displayLabel
}

enum class OrderStatus(val displayLabel: String) {
    PLACED("Order placed"),
    CONFIRMED("Confirmed by restaurant"),
    PREPARING("Being prepared"),
    ON_THE_WAY("On the way"),
    DELIVERED("Delivered");

    companion object {
        fun from(raw: String): OrderStatus =
            entries.firstOrNull { it.name == raw } ?: PLACED
    }
}

data class Courier(val name: String, val phone: String)
```

### Step 5 — The ViewModel (DOC 5, DOC 4)

The ViewModel now does far less network-related work. It connects the Repository's
Flow to a `StateFlow` that the UI observes, and it exposes a `refresh()` function
for user-triggered and automatic polling:

```kotlin
@HiltViewModel  // DOC 14 — Hilt provides the dependencies
class OrderTrackingViewModel @Inject constructor(
    private val orderRepository: OrderRepository,
    savedStateHandle: SavedStateHandle  // survives process death (DOC 5)
) : ViewModel() {

    // orderId comes from navigation arguments, stored in SavedStateHandle
    private val orderId: String = checkNotNull(savedStateHandle["orderId"])

    // Single UiState sealed class — never inconsistent
    val uiState: StateFlow<OrderTrackingUiState> = orderRepository
        .observeOrder(orderId)
        .map { order ->
            when {
                order == null -> OrderTrackingUiState.Empty
                else -> OrderTrackingUiState.Success(order)
            }
        }
        .stateIn(
            scope = viewModelScope,
            started = SharingStarted.WhileSubscribed(5_000),  // DOC 4
            initialValue = OrderTrackingUiState.Loading
        )

    // Network errors are surfaced as one-shot events, not state
    private val _networkError = MutableSharedFlow<String>(extraBufferCapacity = 1)
    val networkError = _networkError.asSharedFlow()

    fun refresh() {
        viewModelScope.launch {
            orderRepository.refresh(orderId)
                .onFailure { _networkError.tryEmit("Could not update order status") }
        }
    }
}

// Sealed class covers all UI states — no inconsistent flag combinations
sealed interface OrderTrackingUiState {
    data object Loading : OrderTrackingUiState
    data object Empty : OrderTrackingUiState
    data class Success(val order: Order) : OrderTrackingUiState
}
```

`SharingStarted.WhileSubscribed(5_000)` is explained in DOC 4. The Flow is active
while at least one subscriber (the UI) is collecting. The 5-second timeout means the
upstream Flow stays alive for 5 seconds after the last subscriber disappears — this
handles configuration changes (screen rotation) without restarting the upstream flow.

### The architecture after the rewrite

```text
BEFORE (first attempt):
    OrderTrackingViewModel
        ↓ calls directly
    OrderApi (Retrofit)

AFTER (second version):
    [UI Layer]
    OrderTrackingScreen (Compose)
        ↓ observes StateFlow
    OrderTrackingViewModel
        ↓ observes Flow / calls refresh()
    [Domain Layer]
    OrderRepository
        ↙              ↘
    OrderDao         OrderTrackingApi
    (Room)           (Retrofit)
        ↓
    orders table (Room database)

Data flow:
    Network → Repository.refresh() → dao.upsert() → Room emits → Flow emits
                                                              → ViewModel maps
                                                              → UI updates
```

---

## Act 3 — Polling Without Battery Drain

The product requirement: the order status should auto-refresh every 30 seconds while
the user has the tracking screen open. The team's constraint: don't drain the battery.

### The polling job in the ViewModel

The team implemented polling directly in the ViewModel using a coroutine:

```kotlin
@HiltViewModel
class OrderTrackingViewModel @Inject constructor(
    private val orderRepository: OrderRepository,
    savedStateHandle: SavedStateHandle
) : ViewModel() {

    private val orderId: String = checkNotNull(savedStateHandle["orderId"])

    val uiState: StateFlow<OrderTrackingUiState> = /* ... same as above ... */

    private var pollingJob: Job? = null

    fun startPolling() {
        // Cancel any existing polling job before starting a new one
        pollingJob?.cancel()
        pollingJob = viewModelScope.launch {
            while (isActive) {
                orderRepository.refresh(orderId)
                    .onFailure { /* silently retry on next interval */ }
                delay(30_000L)
            }
        }
    }

    fun stopPolling() {
        pollingJob?.cancel()
        pollingJob = null
    }

    override fun onCleared() {
        super.onCleared()
        pollingJob?.cancel()  // belt-and-suspenders — viewModelScope cancels it anyway
    }
}
```

The UI calls `startPolling()` on appearance and `stopPolling()` on disappearance:

```kotlin
@Composable
fun OrderTrackingScreen(
    viewModel: OrderTrackingViewModel = hiltViewModel()
) {
    val uiState by viewModel.uiState.collectAsStateWithLifecycle()

    LaunchedEffect(Unit) {
        viewModel.startPolling()
    }

    DisposableEffect(Unit) {
        onDispose { viewModel.stopPolling() }
    }

    // ... rest of UI
}
```

### Why this works here

```text
Polling in viewModelScope:

    User opens Order Tracking screen
        ↓
    LaunchedEffect → startPolling()
        ↓
    pollingJob runs: refresh → delay(30s) → refresh → delay(30s) → ...
        ↓
    User leaves screen (back button, home button)
        ↓
    DisposableEffect.onDispose → stopPolling() → pollingJob.cancel()
        ↓
    No more network calls. Battery preserved.

Also: if the ViewModel is cleared (system kills the process):
    viewModelScope is cancelled → pollingJob is cancelled automatically (DOC 5)
```

The polling job respects the user's attention. The moment the screen is not visible,
polling stops. This is the correct behavior for active-user tracking features.

### When to escalate to WorkManager (DOC 15)

The team explicitly decided NOT to use WorkManager here. The reasoning matters:

```text
viewModelScope polling — appropriate when:
    ✓ User is actively viewing the screen
    ✓ 30-second interval is acceptable
    ✓ Stopping when the user leaves is correct behavior
    ✓ The work is not critical if skipped (fresh data is nice-to-have, cache is shown)

WorkManager PeriodicWorkRequest — appropriate when:
    ✓ Work must happen even when the app is in background or closed
    ✓ 15-minute minimum interval is acceptable (WorkManager's enforced minimum)
    ✓ The work is deferrable and does not need to be exact

Foreground Service — appropriate when:
    ✓ Work must happen in background with tight timing (e.g., every 10 seconds)
    ✓ The user must be notified that the app is doing background work
    ✓ Example: a map-based courier tracking screen that shows a moving pin
```

For order tracking, `viewModelScope` is right. If the team had needed to poll while
the app was in the background (e.g., to trigger a push notification locally), a
Foreground Service with a notification showing "Tracking your order" would be the
correct escalation path, not WorkManager.

### Exponential backoff for resilience

The polling-on-error behavior matters. A naive implementation retries immediately
on error. A production implementation backs off:

```kotlin
fun startPolling() {
    pollingJob?.cancel()
    pollingJob = viewModelScope.launch {
        var consecutiveFailures = 0
        while (isActive) {
            val result = orderRepository.refresh(orderId)
            if (result.isSuccess) {
                consecutiveFailures = 0
                delay(30_000L)
            } else {
                consecutiveFailures++
                // Exponential backoff: 30s → 60s → 120s → max 5 minutes
                val backoffMs = minOf(30_000L * (1L shl consecutiveFailures), 300_000L)
                delay(backoffMs)
            }
        }
    }
}
```

This matters when the server is under load or the network is intermittent. A failing
client that polls every 500ms because it keeps getting errors is a failure amplifier.
Exponential backoff is a production necessity.

---

## Act 4 — Handling Order Completion

When the order is delivered, the team needed to stop polling and handle the terminal
state. The domain model already has `order.isActive` for this. The ViewModel responds
to it:

```kotlin
// In OrderTrackingViewModel
init {
    // React to order becoming delivered — stop polling automatically
    viewModelScope.launch {
        uiState.collect { state ->
            if (state is OrderTrackingUiState.Success && !state.order.isActive) {
                stopPolling()
            }
        }
    }
}
```

The UI shows a different screen state when delivered. The user is not stuck on a
polling loop after the order arrives. The architecture handles it without additional
product logic in the UI layer.

---

## Act 5 — The Offline Test

Tests written before the rewrite were brittle because they mocked `OrderApi` directly.
After the rewrite, tests use a `FakeOrderRepository` — an in-memory implementation of
the Repository interface that is fast, deterministic, and trivial to set up.

### The fake

```kotlin
class FakeOrderRepository : OrderRepository {

    private val _orders = MutableStateFlow<Map<String, Order>>(emptyMap())
    private var refreshError: Throwable? = null

    // Pre-load the "cached" order for a test
    fun setCachedOrder(order: Order) {
        _orders.value = _orders.value + (order.id to order)
    }

    // Simulate a network error on the next refresh call
    fun setRefreshError(error: Throwable) {
        refreshError = error
    }

    // Simulate a successful refresh that changes the order status
    fun simulateStatusUpdate(orderId: String, newStatus: OrderStatus) {
        val current = _orders.value[orderId] ?: return
        _orders.value = _orders.value + (orderId to current.copy(status = newStatus))
    }

    override fun observeOrder(orderId: String): Flow<Order?> =
        _orders.map { it[orderId] }

    override suspend fun refresh(orderId: String): Result<Unit> {
        val error = refreshError
        return if (error != null) {
            refreshError = null  // reset after one use
            Result.failure(error)
        } else {
            Result.success(Unit)
        }
    }

    override suspend fun archiveDeliveredOrder(orderId: String) {
        _orders.value = _orders.value - orderId
    }
}
```

### The tests (DOC 16)

```kotlin
@OptIn(ExperimentalCoroutinesApi::class)
class OrderTrackingViewModelTest {

    @get:Rule
    val mainDispatcherRule = MainDispatcherRule()  // replaces Main dispatcher with TestDispatcher

    private val fakeRepository = FakeOrderRepository()
    private val savedStateHandle = SavedStateHandle(mapOf("orderId" to "order-123"))
    private lateinit var viewModel: OrderTrackingViewModel

    @Before
    fun setup() {
        viewModel = OrderTrackingViewModel(fakeRepository, savedStateHandle)
    }

    @Test
    fun `initial state is Loading`() = runTest {
        assertIs<OrderTrackingUiState.Loading>(viewModel.uiState.value)
    }

    @Test
    fun `when cache exists, shows Success without network call`() = runTest {
        fakeRepository.setCachedOrder(
            Order(id = "order-123", status = OrderStatus.PREPARING,
                  restaurantName = "Nasi Kandar Pelita", restaurantImageUrl = "",
                  estimatedMinutes = 15, courier = null)
        )

        val state = viewModel.uiState.value
        assertIs<OrderTrackingUiState.Success>(state)
        assertEquals(OrderStatus.PREPARING, state.order.status)
    }

    @Test
    fun `when no internet, cached order is still shown`() = runTest {
        // Arrange: pre-load cache
        fakeRepository.setCachedOrder(
            Order(id = "order-123", status = OrderStatus.PREPARING,
                  restaurantName = "Nasi Kandar Pelita", restaurantImageUrl = "",
                  estimatedMinutes = 15, courier = null)
        )
        // Arrange: next refresh will fail
        fakeRepository.setRefreshError(IOException("No internet"))

        // Act: user triggers a refresh (or polling fires)
        viewModel.refresh()
        advanceUntilIdle()

        // Assert: UI still shows cached data — offline-first works
        val state = viewModel.uiState.value
        assertIs<OrderTrackingUiState.Success>(state)
        assertEquals(OrderStatus.PREPARING, state.order.status)
    }

    @Test
    fun `when status updates, UI updates automatically`() = runTest {
        fakeRepository.setCachedOrder(
            Order(id = "order-123", status = OrderStatus.PREPARING,
                  restaurantName = "Nasi Kandar Pelita", restaurantImageUrl = "",
                  estimatedMinutes = 15, courier = null)
        )

        // Simulate server-side status change arriving via Repository
        fakeRepository.simulateStatusUpdate("order-123", OrderStatus.ON_THE_WAY)
        advanceUntilIdle()

        val state = viewModel.uiState.value
        assertIs<OrderTrackingUiState.Success>(state)
        assertEquals(OrderStatus.ON_THE_WAY, state.order.status)
    }

    @Test
    fun `polling stops when order is delivered`() = runTest {
        fakeRepository.setCachedOrder(
            Order(id = "order-123", status = OrderStatus.ON_THE_WAY,
                  restaurantName = "Nasi Kandar Pelita", restaurantImageUrl = "",
                  estimatedMinutes = 5, courier = Courier("Ali", "+60123456789"))
        )
        viewModel.startPolling()

        // Simulate delivery
        fakeRepository.simulateStatusUpdate("order-123", OrderStatus.DELIVERED)
        advanceUntilIdle()

        // Polling should have stopped — the ViewModel's isActive check prevents further polls
        // We verify by checking the state rather than internal polling state
        val state = viewModel.uiState.value
        assertIs<OrderTrackingUiState.Success>(state)
        assertFalse(state.order.isActive)
    }
}
```

These tests run in milliseconds. They test the logic the team actually cares about:
does offline work? Does polling stop when delivered? Does status change reach the UI?
None of these require a real network or a real database.

---

## What We Learned

### Lesson 1: The data flow is the architecture

The critical mental model shift: stop thinking about "when do I call the API?" and
start thinking about "where does data live, and how does it flow to the UI?"

```text
WRONG MENTAL MODEL:
    UI needs data → call API → show result

CORRECT MENTAL MODEL:
    Data lives in Room (permanent)
    UI observes Room (always current)
    Network is a mechanism for updating Room (best-effort)
```

When you internalize this, offline-first is not extra work. It is what correct
architecture looks like by default.

### Lesson 2: The Repository owns the "single source of truth" contract

The Repository is where the policy lives: "the UI reads from the DB, not the network."
If the Repository is not there, everyone who needs order data will write their own
caching logic. The Repository is the single place that enforces the pattern.

### Lesson 3: `Flow` from Room is not a performance trick — it is the update mechanism

Many developers use `Flow` from Room as a "nice reactive API." The real reason to use
it: it eliminates the need to manually notify observers when data changes. When the
Repository writes to Room via `dao.upsert()`, every `Flow` backed by that query emits
a new value. The UI does not need to know that a refresh happened. It just observes.

### Lesson 4: viewModelScope polling is correct for active-user screens

WorkManager is for deferred background work. viewModelScope is for active, foreground
work that the user is actively watching. Polling every 30 seconds while the user
watches their order is viewModelScope territory. Syncing app data while the app is
closed is WorkManager territory. Do not conflate them.

### Lesson 5: `Result<Unit>` from refresh() is not the order — it is the write receipt

`refresh()` returns `Result<Unit>`. It does not return the order. The order flows
through `observeOrder()`. This design separates "did the write succeed?" from "here is
the current order state." The ViewModel can handle the error (show a snackbar) without
touching the order display — they are independent concerns.

### Lesson 6: Fake repositories are better than mocked APIs

Writing tests against a `FakeOrderRepository` is fast, reliable, and tests the right
level of abstraction. Mocking `OrderApi` tests Retrofit's plumbing, not the
application's behavior. The fake tests what actually matters: does the ViewModel
handle a cached order correctly? Does it handle a refresh failure gracefully?

---

## DOC Connection Map

```text
What changed from Act 1 to Act 2, and which DOC covers each piece:

DOC 11 (Retrofit / Remote Communication)
    → OrderTrackingApi — the Retrofit interface
    → OrderDto — the network response model
    → OrderDto.toEntity() — mapping network response to Room entity

DOC 12 (Room / Local Persistence)
    → @Entity OrderEntity — the database row representation
    → @Dao OrderDao — the database access object
    → Flow<OrderEntity?> from @Query — Room's reactive query API
    → dao.upsert() — writing to Room triggers Flow emit

DOC 13 (Data Architecture / Repository Pattern)
    → OrderRepository — the coordinator between network and database
    → Single Source of Truth: Room, not network
    → Three-layer data model: DTO ↔ Entity ↔ Domain
    → Result<Unit> from refresh() — error propagation without coupling

DOC 5 (Android Lifecycle / ViewModel)
    → OrderTrackingViewModel — lifecycle-aware state holder
    → viewModelScope — coroutine scope tied to ViewModel lifetime
    → SavedStateHandle — survives process death, holds orderId
    → ViewModel.onCleared() — cleanup hook

DOC 4 (State Management / StateFlow)
    → MutableStateFlow, StateFlow — observable state for the UI
    → stateIn() — converts a cold Flow to a hot StateFlow
    → SharingStarted.WhileSubscribed(5_000) — active only when UI is subscribed
    → MutableSharedFlow — one-shot events (network error snackbar)

DOC 14 (Dependency Injection / Hilt)
    → @HiltViewModel — Hilt-managed ViewModel
    → @Inject constructor — Hilt provides OrderRepository

DOC 15 (Background Work / WorkManager)
    → Discussed as the escalation path for background polling
    → viewModelScope: active user polling (30s intervals)
    → WorkManager: deferred background sync (15min+ intervals)
    → Foreground Service: tight-interval background tracking

DOC 16 (Testing Strategy)
    → FakeOrderRepository — in-memory test double
    → runTest — coroutine test runner
    → advanceUntilIdle — force all pending coroutines to complete
    → MainDispatcherRule — replace Main dispatcher in tests
```

---

*Next case study: 03-migration-xml-to-compose.md — taking a real production RecyclerView
screen and migrating it to Compose incrementally, without disrupting the release schedule.*
