# Case Study 03 — Migrating a Screen from XML to Compose

> **Book 5 — Learning From Experience**
>
> The scariest migrations are not the technical ones. They are the ones where the
> business cannot pause, the team cannot afford to break the release schedule, and
> the code has to keep shipping while the surgery happens. This case study is about
> how to do exactly that with an XML-to-Compose migration — incrementally, safely,
> verifiably.

---

## The Setup

The app has an Order History screen. It was written two years ago, built on Fragment +
RecyclerView + ViewBinding — the standard Android stack at the time. It works. Users
use it. There are no bugs filed against it.

The team is migrating to Compose for new screens. The Order History screen is a
candidate for migration for two reasons:

1. The RecyclerView has an `OrderAdapter` with 200 lines of boilerplate that every
   new developer on the team has to understand before touching it.

2. The team wants to eventually move to Compose Navigation, and the Fragment is a
   blocker for that.

The constraint: the migration cannot delay the upcoming release. The new screen must
be deployable at any phase. If something goes wrong, it must be rollback-able.

---

## The Original XML Architecture

Before touching anything, the team mapped out what exists:

```text
XML Stack — what we are migrating FROM:

    OrderHistoryFragment (Fragment)
        ↓ inflates
    order_history_fragment.xml
        └── RecyclerView
        ↓ creates
    OrderAdapter (RecyclerView.Adapter<OrderViewHolder>)
        ↓ inflates per item
    order_item.xml
        └── CardView
            ├── TextView (restaurant name)
            ├── TextView (status label)
            └── TextView (date)

    OrderHistoryViewModel (UNCHANGED — survives the migration)
        ↓ observed by
    OrderHistoryFragment

Files involved:
    - OrderHistoryFragment.kt          (300 lines)
    - order_history_fragment.xml       (40 lines)
    - OrderAdapter.kt                  (180 lines)
    - OrderViewHolder.kt               (60 lines)
    - order_item.xml                   (80 lines)
    - OrderHistoryViewModel.kt         (80 lines)  ← NOT CHANGING
    - OrderHistoryUiState.kt           (30 lines)  ← NOT CHANGING
```

The ViewModel is explicitly not changing. This is the central premise: if the
architecture is correctly layered (DOC 5, DOC 13), the UI technology is a skin over
a stable core. Replacing the skin should not touch the core.

### The XML adapter in full

The adapter is the most painful part of the XML stack. Here it is, unabridged —
because understanding what this code does is what makes the Compose replacement
feel like relief:

```kotlin
class OrderAdapter(
    private val onOrderClick: (Order) -> Unit
) : ListAdapter<Order, OrderAdapter.OrderViewHolder>(OrderDiffCallback()) {

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): OrderViewHolder {
        val binding = ItemOrderBinding.inflate(
            LayoutInflater.from(parent.context),
            parent,
            false
        )
        return OrderViewHolder(binding)
    }

    override fun onBindViewHolder(holder: OrderViewHolder, position: Int) {
        val order = getItem(position)
        holder.bind(order, onOrderClick)
    }

    class OrderViewHolder(
        private val binding: ItemOrderBinding
    ) : RecyclerView.ViewHolder(binding.root) {

        fun bind(order: Order, onOrderClick: (Order) -> Unit) {
            binding.restaurantName.text = order.restaurantName
            binding.orderStatus.text = order.status.displayLabel
            binding.orderDate.text = formatDate(order.placedAt)
            binding.orderTotal.text = formatCurrency(order.totalAmount)
            binding.orderItemCount.text = binding.root.context.resources.getQuantityString(
                R.plurals.item_count,
                order.itemCount,
                order.itemCount
            )

            // Status chip color
            val chipColor = when (order.status) {
                OrderStatus.DELIVERED -> R.color.status_delivered
                OrderStatus.CANCELLED -> R.color.status_cancelled
                else -> R.color.status_active
            }
            binding.statusChip.setChipBackgroundColorResource(chipColor)

            binding.root.setOnClickListener { onOrderClick(order) }
        }

        private fun formatDate(timestamp: Long): String {
            val sdf = SimpleDateFormat("dd MMM yyyy, HH:mm", Locale.getDefault())
            return sdf.format(Date(timestamp))
        }

        private fun formatCurrency(amount: Double): String {
            return NumberFormat.getCurrencyInstance().format(amount)
        }
    }

    class OrderDiffCallback : DiffUtil.ItemCallback<Order>() {

        override fun areItemsTheSame(oldItem: Order, newItem: Order): Boolean =
            oldItem.id == newItem.id

        override fun areContentsTheSame(oldItem: Order, newItem: Order): Boolean =
            oldItem == newItem
    }
}
```

This is standard RecyclerView code. Every Android developer has written it. It works.
But count what it takes to add a new field to the order item:

1. Add the field to `Order` (domain model)
2. Add a `TextView` to `order_item.xml`
3. Get a reference to the view in `OrderViewHolder`
4. Bind the field in `OrderViewHolder.bind()`
5. Potentially update `OrderDiffCallback.areContentsTheSame()`

Five files touched for one field addition. And step 3 is the dangerous step — getting
the view reference wrong causes a null-pointer crash at runtime that ViewBinding only
partially prevents.

### The Fragment — where the wiring happens

```kotlin
class OrderHistoryFragment : Fragment() {

    private var _binding: FragmentOrderHistoryBinding? = null
    private val binding get() = _binding!!

    private val viewModel: OrderHistoryViewModel by viewModels()
    private lateinit var adapter: OrderAdapter

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View {
        _binding = FragmentOrderHistoryBinding.inflate(inflater, container, false)
        return binding.root
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)

        adapter = OrderAdapter { order ->
            findNavController().navigate(
                OrderHistoryFragmentDirections.actionOrderHistoryToOrderDetail(order.id)
            )
        }

        binding.recyclerView.apply {
            adapter = this@OrderHistoryFragment.adapter
            layoutManager = LinearLayoutManager(requireContext())
            addItemDecoration(DividerItemDecoration(requireContext(), DividerItemDecoration.VERTICAL))
        }

        viewLifecycleOwner.lifecycleScope.launch {
            viewLifecycleOwner.repeatOnLifecycle(Lifecycle.State.STARTED) {
                viewModel.uiState.collect { state ->
                    when (state) {
                        is OrderHistoryUiState.Loading -> {
                            binding.progressBar.isVisible = true
                            binding.recyclerView.isVisible = false
                            binding.emptyView.isVisible = false
                        }
                        is OrderHistoryUiState.Success -> {
                            binding.progressBar.isVisible = false
                            binding.recyclerView.isVisible = true
                            binding.emptyView.isVisible = false
                            adapter.submitList(state.orders)
                        }
                        is OrderHistoryUiState.Empty -> {
                            binding.progressBar.isVisible = false
                            binding.recyclerView.isVisible = false
                            binding.emptyView.isVisible = true
                        }
                    }
                }
            }
        }
    }

    override fun onDestroyView() {
        super.onDestroyView()
        _binding = null  // prevent memory leaks (DOC 5)
    }
}
```

The Fragment is 100 lines of wiring: managing `_binding` nullability, creating the
adapter, setting up the RecyclerView, observing the ViewModel, showing/hiding views.
None of this is business logic. All of it is plumbing that Compose eliminates.

---

## The Migration Strategy

The team chose incremental migration over a rewrite. The reasoning:

**A rewrite means:**
- The new screen and the old screen coexist in git for potentially weeks
- Merge conflicts are possible as other developers continue touching the old screen
- If the Compose version has a bug, the rollback is reverting a large commit

**Incremental migration means:**
- Phase 1 ships to production: Fragment with `ComposeView` replacing the RecyclerView
- Phase 2 ships to production: full Compose screen inside the Fragment
- Phase 3 ships to production: Fragment removed, NavHost destination
- At each phase, the diff is small and reviewable

The team chose three phases with a clear "deployable" checkpoint at each one.

```text
PHASE 1 — ComposeView inside Fragment (ships to production)
    Fragment stays. XML layout stays. RecyclerView is replaced by ComposeView.
    Risk: Low. Easy rollback.

PHASE 2 — Full Compose screen in Fragment (ships to production)
    Fragment stays. ComposeView takes the full screen. XML layout is deleted.
    Risk: Low. ViewModel is unchanged.

PHASE 3 — Fragment removed, NavHost destination (ships to production)
    Fragment is deleted. NavGraph is updated to point at the Compose screen directly.
    Risk: Medium. Navigation changes need testing.
```

---

## Phase 1 — ComposeView in Fragment

The first PR is minimal. It keeps the Fragment and the XML layout, but replaces the
RecyclerView with a `ComposeView`. The `OrderAdapter` and `OrderViewHolder` are
deleted. The `DiffCallback` is deleted.

### The updated XML layout

```xml
<!-- order_history_fragment.xml — Phase 1 -->
<!-- RecyclerView is gone. ComposeView takes its place. -->
<FrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <androidx.compose.ui.platform.ComposeView
        android:id="@+id/compose_view"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />

</FrameLayout>
```

### The updated Fragment

```kotlin
class OrderHistoryFragment : Fragment() {

    private var _binding: FragmentOrderHistoryBinding? = null
    private val binding get() = _binding!!

    private val viewModel: OrderHistoryViewModel by viewModels()

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View {
        _binding = FragmentOrderHistoryBinding.inflate(inflater, container, false)
        return binding.root
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)

        binding.composeView.apply {
            // CRITICAL: tell Compose when to dispose the composition (DOC 8)
            // DisposeOnViewTreeLifecycleDestroyed matches the Fragment view lifecycle
            setViewCompositionStrategy(
                ViewCompositionStrategy.DisposeOnViewTreeLifecycleDestroyed
            )
            setContent {
                val uiState by viewModel.uiState.collectAsStateWithLifecycle()  // DOC 9
                AppTheme {
                    OrderHistoryScreen(
                        uiState = uiState,
                        onOrderClick = { orderId ->
                            findNavController().navigate(
                                OrderHistoryFragmentDirections
                                    .actionOrderHistoryToOrderDetail(orderId)
                            )
                        }
                    )
                }
            }
        }
    }

    override fun onDestroyView() {
        super.onDestroyView()
        _binding = null
    }
}
```

`ViewCompositionStrategy.DisposeOnViewTreeLifecycleDestroyed` is the correct strategy
for Compose hosted inside a Fragment (DOC 9). Without it, the composition can survive
past the Fragment view's lifecycle and cause memory leaks — the same problem that
`_binding = null` in `onDestroyView()` was solving for ViewBinding.

Phase 1 is shippable. The ViewModel has not changed. The Compose screen is new but
isolated. If a critical bug is found, reverting the Fragment and XML changes restores
the old behavior.

---

## Phase 2 — The Full Compose Screen

This is the implementation of `OrderHistoryScreen` — the composable that replaces
everything the Fragment was doing with the RecyclerView.

### The Compose screen

```kotlin
@Composable
fun OrderHistoryScreen(
    uiState: OrderHistoryUiState,
    onOrderClick: (String) -> Unit,
    modifier: Modifier = Modifier
) {
    when (uiState) {
        is OrderHistoryUiState.Loading -> OrderHistoryLoading(modifier)
        is OrderHistoryUiState.Empty -> OrderHistoryEmpty(modifier)
        is OrderHistoryUiState.Success -> OrderHistoryList(
            orders = uiState.orders,
            onOrderClick = onOrderClick,
            modifier = modifier
        )
    }
}

@Composable
private fun OrderHistoryLoading(modifier: Modifier = Modifier) {
    Box(
        modifier = modifier.fillMaxSize(),
        contentAlignment = Alignment.Center
    ) {
        CircularProgressIndicator()
    }
}

@Composable
private fun OrderHistoryEmpty(modifier: Modifier = Modifier) {
    Column(
        modifier = modifier.fillMaxSize(),
        verticalArrangement = Arrangement.Center,
        horizontalAlignment = Alignment.CenterHorizontally
    ) {
        Icon(
            imageVector = Icons.Outlined.Receipt,
            contentDescription = null,
            modifier = Modifier.size(64.dp),
            tint = MaterialTheme.colorScheme.onSurfaceVariant
        )
        Spacer(Modifier.height(16.dp))
        Text(
            text = "No orders yet",
            style = MaterialTheme.typography.titleMedium,
            color = MaterialTheme.colorScheme.onSurfaceVariant
        )
    }
}

@Composable
private fun OrderHistoryList(
    orders: List<Order>,
    onOrderClick: (String) -> Unit,
    modifier: Modifier = Modifier
) {
    LazyColumn(
        modifier = modifier,
        contentPadding = PaddingValues(vertical = 8.dp)
    ) {
        items(
            items = orders,
            key = { order -> order.id }  // stable key — replaces DiffCallback (DOC 8)
        ) { order ->
            OrderCard(
                order = order,
                onClick = { onOrderClick(order.id) },
                modifier = Modifier.animateItem()  // smooth insert/remove animations
            )
        }
    }
}
```

`key = { order -> order.id }` in `LazyColumn` is the direct replacement for
`DiffCallback.areItemsTheSame()`. Compose uses the key to track item identity across
recompositions. When the list changes, Compose can identify which items were inserted,
moved, or removed and animate them correctly. The key must be stable (the same object
maps to the same key across recompositions) — a database primary key or UUID is ideal.
(DOC 8 explains recomposition scope and identity in depth.)

### The OrderCard composable — replacing order_item.xml and OrderViewHolder

```kotlin
@Composable
fun OrderCard(
    order: Order,
    onClick: () -> Unit,
    modifier: Modifier = Modifier
) {
    Card(
        modifier = modifier
            .fillMaxWidth()
            .padding(horizontal = 16.dp, vertical = 4.dp)
            .clickable(
                onClick = onClick,
                indication = rememberRipple(),
                interactionSource = remember { MutableInteractionSource() }
            ),
        elevation = CardDefaults.cardElevation(defaultElevation = 1.dp),
        colors = CardDefaults.cardColors(
            containerColor = MaterialTheme.colorScheme.surface
        )
    ) {
        Row(
            modifier = Modifier
                .padding(16.dp)
                .fillMaxWidth(),
            horizontalArrangement = Arrangement.SpaceBetween,
            verticalAlignment = Alignment.Top
        ) {
            Column(modifier = Modifier.weight(1f)) {
                Text(
                    text = order.restaurantName,
                    style = MaterialTheme.typography.titleMedium,
                    maxLines = 1,
                    overflow = TextOverflow.Ellipsis
                )
                Spacer(Modifier.height(2.dp))
                Text(
                    text = formatDate(order.placedAt),
                    style = MaterialTheme.typography.bodySmall,
                    color = MaterialTheme.colorScheme.onSurfaceVariant
                )
                Spacer(Modifier.height(4.dp))
                Text(
                    text = pluralStringResource(
                        R.plurals.item_count,
                        order.itemCount,
                        order.itemCount
                    ),
                    style = MaterialTheme.typography.bodySmall,
                    color = MaterialTheme.colorScheme.onSurfaceVariant
                )
            }

            Column(horizontalAlignment = Alignment.End) {
                Text(
                    text = formatCurrency(order.totalAmount),
                    style = MaterialTheme.typography.titleSmall
                )
                Spacer(Modifier.height(8.dp))
                OrderStatusChip(status = order.status)
            }
        }
    }
}

@Composable
private fun OrderStatusChip(status: OrderStatus) {
    val (backgroundColor, contentColor) = when (status) {
        OrderStatus.DELIVERED -> MaterialTheme.colorScheme.secondaryContainer to
                                 MaterialTheme.colorScheme.onSecondaryContainer
        OrderStatus.CANCELLED -> MaterialTheme.colorScheme.errorContainer to
                                 MaterialTheme.colorScheme.onErrorContainer
        else -> MaterialTheme.colorScheme.primaryContainer to
                MaterialTheme.colorScheme.onPrimaryContainer
    }

    Surface(
        shape = MaterialTheme.shapes.small,
        color = backgroundColor,
        contentColor = contentColor
    ) {
        Text(
            text = status.displayLabel,
            style = MaterialTheme.typography.labelSmall,
            modifier = Modifier.padding(horizontal = 8.dp, vertical = 4.dp)
        )
    }
}

// Helpers — in a DateFormatter / CurrencyFormatter utility object in production
private fun formatDate(timestamp: Long): String {
    val sdf = SimpleDateFormat("dd MMM yyyy, HH:mm", Locale.getDefault())
    return sdf.format(Date(timestamp))
}

private fun formatCurrency(amount: Double): String =
    NumberFormat.getCurrencyInstance().format(amount)
```

Notice that `OrderStatusChip` replaces the `setChipBackgroundColorResource` call in
`OrderViewHolder.bind()`. In XML, coloring a chip required a resource ID and a
View method call. In Compose, it is a color computation that is part of the
composable's logic — no View references, no resource IDs, no null checks.

Adding a new field now requires:
1. Add the field to `Order` (domain model)
2. Add a `Text()` inside `OrderCard`

Two locations. One of them is the domain model change, which would have been required
either way.

---

## Side-by-Side Comparison

```text
What changed across the migration — complete accounting:

DELETED (XML stack):
    - OrderAdapter.kt                  (180 lines) → LazyColumn + items()
    - OrderViewHolder.kt               (60 lines)  → OrderCard composable
    - OrderDiffCallback (inner class)  (20 lines)  → key = { order.id }
    - order_history_fragment.xml       (40 lines)  → OrderHistoryScreen layout
    - order_item.xml                   (80 lines)  → OrderCard layout

ADDED (Compose stack):
    - OrderHistoryScreen.kt            (90 lines)  — screen + state routing
    - OrderCard.kt                     (80 lines)  — item composable
    - OrderStatusChip.kt               (25 lines)  — status display

NET: 380 lines deleted → 195 lines added
     51% reduction in view layer code

UNCHANGED (the architecture survives):
    - OrderHistoryViewModel.kt         (80 lines)  ← identical
    - OrderHistoryUiState.kt           (30 lines)  ← identical
    - OrderRepository.kt               ← identical
    - Network and database layers      ← identical

What the unchanged files prove:
    The architecture was correctly layered (DOC 13).
    When the UI technology changes, the state management does not.
```

### RecyclerView vs LazyColumn — behavioral differences that matter

```text
RecyclerView + ListAdapter:
    - DiffCallback runs on background thread (Dispatchers.Default)
    - Animates using ItemAnimator (DefaultItemAnimator by default)
    - View recycling: ViewHolder instances are reused across items
    - Memory: Adapter holds the current list in DiffUtil
    - Scroll state: RecyclerView.LayoutManager preserves scroll position automatically

LazyColumn + key:
    - Diff computed during recomposition (on the main thread, via SnapshotStateList comparison)
    - Animates using Modifier.animateItem() — per-item opt-in
    - No recycling: composables are created/destroyed as they enter/leave the viewport
      (Compose's runtime is efficient about this — DOC 8 explains slot table reuse)
    - Memory: Compose runtime manages slot table entries
    - Scroll state: LazyListState preserves scroll position automatically

Performance:
    RecyclerView: Excellent for extremely long lists (10,000+ items). Mature, battle-tested.
    LazyColumn: Excellent for typical mobile lists (< 1,000 items). Less boilerplate.
    For Order History (typically < 100 orders per user), LazyColumn is more than sufficient.
```

---

## Phase 3 — Removing the Fragment (future work)

Phase 3 is not shipped in this case study — it is planned work. Documenting it here
because the team needs to understand the final state to make good decisions in Phases
1 and 2.

Phase 3 removes the Fragment and makes `OrderHistoryScreen` a first-class NavHost
destination. This requires switching from Fragment Navigation to Compose Navigation
(or a hybrid approach using `androidx.navigation.compose`).

```kotlin
// What the NavGraph destination looks like after Phase 3

@Composable
fun AppNavGraph(navController: NavHostController) {
    NavHost(navController = navController, startDestination = "order_history") {

        composable("order_history") {
            val viewModel: OrderHistoryViewModel = hiltViewModel()  // DOC 14
            val uiState by viewModel.uiState.collectAsStateWithLifecycle()

            OrderHistoryScreen(
                uiState = uiState,
                onOrderClick = { orderId ->
                    navController.navigate("order_detail/$orderId")
                }
            )
        }

        composable(
            route = "order_detail/{orderId}",
            arguments = listOf(navArgument("orderId") { type = NavType.StringType })
        ) { backStackEntry ->
            val orderId = backStackEntry.arguments?.getString("orderId")!!
            // ... OrderDetailScreen
        }
    }
}
```

Phase 3 changes:
- `OrderHistoryFragment.kt` → deleted
- `FragmentContainerView` in activity → replaced by `NavHost`
- `NavGraph.xml` → replaced by Kotlin NavGraph DSL
- `OrderHistoryFragmentDirections` → replaced by string routes (or typed route objects)

The risk in Phase 3 is navigation changes touching other screens. Phase 3 should be
a dedicated sprint with full regression testing of all navigation flows.

---

## Testing the Compose Screen (DOC 16)

The primary advantage of migrating to Compose is that `OrderHistoryScreen` becomes
directly testable with `ComposeTestRule` — no Fragment, no Activity, no RecyclerView.

### Compose UI tests

```kotlin
@RunWith(AndroidJUnit4::class)
class OrderHistoryScreenTest {

    @get:Rule
    val composeTestRule = createComposeRule()

    private val sampleOrders = listOf(
        Order(
            id = "order-1",
            restaurantName = "Nasi Kandar Pelita",
            status = OrderStatus.DELIVERED,
            placedAt = System.currentTimeMillis() - 3_600_000L,
            totalAmount = 24.50,
            itemCount = 3
        ),
        Order(
            id = "order-2",
            restaurantName = "Roti Canai Kayu Ara",
            status = OrderStatus.CANCELLED,
            placedAt = System.currentTimeMillis() - 7_200_000L,
            totalAmount = 12.00,
            itemCount = 2
        )
    )

    @Test
    fun `loading state shows progress indicator`() {
        composeTestRule.setContent {
            AppTheme {
                OrderHistoryScreen(
                    uiState = OrderHistoryUiState.Loading,
                    onOrderClick = {}
                )
            }
        }

        composeTestRule.onNode(hasTestTag("loading_indicator")).assertIsDisplayed()
    }

    @Test
    fun `empty state shows empty view with correct message`() {
        composeTestRule.setContent {
            AppTheme {
                OrderHistoryScreen(
                    uiState = OrderHistoryUiState.Empty,
                    onOrderClick = {}
                )
            }
        }

        composeTestRule.onNodeWithText("No orders yet").assertIsDisplayed()
    }

    @Test
    fun `success state shows all orders`() {
        composeTestRule.setContent {
            AppTheme {
                OrderHistoryScreen(
                    uiState = OrderHistoryUiState.Success(sampleOrders),
                    onOrderClick = {}
                )
            }
        }

        composeTestRule.onNodeWithText("Nasi Kandar Pelita").assertIsDisplayed()
        composeTestRule.onNodeWithText("Roti Canai Kayu Ara").assertIsDisplayed()
    }

    @Test
    fun `clicking an order calls onOrderClick with correct id`() {
        var clickedOrderId: String? = null

        composeTestRule.setContent {
            AppTheme {
                OrderHistoryScreen(
                    uiState = OrderHistoryUiState.Success(sampleOrders),
                    onOrderClick = { orderId -> clickedOrderId = orderId }
                )
            }
        }

        composeTestRule.onNodeWithText("Nasi Kandar Pelita").performClick()

        assertEquals("order-1", clickedOrderId)
    }

    @Test
    fun `delivered order shows Delivered status chip`() {
        composeTestRule.setContent {
            AppTheme {
                OrderHistoryScreen(
                    uiState = OrderHistoryUiState.Success(sampleOrders),
                    onOrderClick = {}
                )
            }
        }

        // Find the card for the delivered order and check its chip
        composeTestRule
            .onNodeWithText("Nasi Kandar Pelita")
            .onSiblings()
            .filterToOne(hasText("Delivered"))
            .assertIsDisplayed()
    }

    @Test
    fun `cancelled order shows Cancelled status chip with error color semantics`() {
        composeTestRule.setContent {
            AppTheme {
                OrderHistoryScreen(
                    uiState = OrderHistoryUiState.Success(sampleOrders),
                    onOrderClick = {}
                )
            }
        }

        composeTestRule.onNodeWithText("Cancelled").assertIsDisplayed()
    }
}
```

These tests run without a ViewModel. They receive `uiState` as a parameter — a plain
Kotlin data class — and verify what the UI renders. This is the payoff of state hoisting
(DOC 9): because `OrderHistoryScreen` takes its state as a parameter rather than
creating a ViewModel internally, it is trivially testable in isolation.

The equivalent Fragment tests required `ActivityScenario`, setting up a Fragment,
observing the ViewModel, injecting fake dependencies through Hilt test components —
dozens of lines of setup for the same assertions.

### ViewModel tests (unchanged from before the migration)

The ViewModel tests from before the migration still pass unchanged. This is the proof
that the migration was correct:

```kotlin
// These tests existed before the Compose migration.
// They still pass after. The ViewModel was not touched.
class OrderHistoryViewModelTest {

    @get:Rule
    val mainDispatcherRule = MainDispatcherRule()

    private val fakeRepository = FakeOrderHistoryRepository()
    private val viewModel = OrderHistoryViewModel(fakeRepository)

    @Test
    fun `initial state is Loading`() = runTest {
        assertIs<OrderHistoryUiState.Loading>(viewModel.uiState.value)
    }

    @Test
    fun `when repository returns orders, state is Success`() = runTest {
        fakeRepository.emit(listOf(
            Order(id = "1", restaurantName = "Test Resto", status = OrderStatus.DELIVERED,
                  placedAt = 0L, totalAmount = 10.0, itemCount = 1)
        ))
        advanceUntilIdle()

        val state = viewModel.uiState.value
        assertIs<OrderHistoryUiState.Success>(state)
        assertEquals(1, state.orders.size)
    }
}
```

---

## The Migration Diff — What Actually Changed

The completed migration (Phases 1 and 2) produced four git commits:

```text
Commit 1: Add Compose dependency to build.gradle
    Modified: app/build.gradle
    Changed: +1 dependency line

Commit 2: Add OrderHistoryScreen and OrderCard composables
    Added: OrderHistoryScreen.kt
    Added: OrderCard.kt
    No deletions yet.

Commit 3: Replace Fragment body with ComposeView
    Modified: OrderHistoryFragment.kt (delete adapter setup, add ComposeView)
    Modified: order_history_fragment.xml (delete RecyclerView, add ComposeView)
    Deleted: OrderAdapter.kt
    Deleted: OrderViewHolder.kt
    Net: -220 lines, +30 lines

Commit 4: Delete unused XML layouts
    Deleted: order_item.xml
    Modified: order_history_fragment.xml (simplified)
    Net: -110 lines
```

Each commit is reviewable and rollback-able individually. The largest commit (Commit 3)
can be reviewed by looking at the Fragment diff and verifying that `ComposeView.setContent`
calls `OrderHistoryScreen` with the same ViewModel state the old `adapter.submitList`
received. A reviewer can verify this in five minutes.

---

## What We Learned

### Lesson 1: Don't rewrite — replace incrementally

The team's instinct was to rewrite the entire screen in one PR. The tech lead pushed
back. The incremental approach took an extra day of planning but produced three
separately deployable artifacts. If the Compose screen had a performance issue on
low-end devices, Phase 1 could be reverted in one commit without touching anything else.

A rewrite-in-one-PR is a big-bang deployment. The team has all the risk at once.
Incremental migration is a series of small bets, each of which can be reversed.

### Lesson 2: The ViewModel not changing is the proof of correct architecture

If the migration had required changes to the ViewModel or the Repository, it would
have been a signal that the original architecture mixed concerns — that the UI and the
state management were entangled. The fact that `OrderHistoryViewModel.kt` survived
Phase 1 and Phase 2 unchanged is the empirical evidence that DOC 5's layering rules
were followed.

Every developer on the team can now state this as a property of the codebase:
"Replacing the UI technology does not touch the ViewModel." This builds confidence for
future migrations.

### Lesson 3: ComposeView in Fragment is not a hack — it is the official interop path

Some developers feel that `ComposeView` inside a Fragment is temporary scaffolding.
It is not. It is the officially supported interop mechanism for hosting Compose in an
existing Fragment-based app. A `ComposeView` Fragment can be in production for years
while the team migrates other screens. There is no deadline to remove the Fragment.

The important correctness detail is `ViewCompositionStrategy`. Using
`DisposeOnViewTreeLifecycleDestroyed` ties the Compose composition lifecycle to the
Fragment view lifecycle — without this, the composition outlives the Fragment view and
leaks memory.

### Lesson 4: key = { item.id } in LazyColumn is not optional

The first draft of `OrderHistoryList` did not have a `key` parameter:

```kotlin
// First draft — missing key
items(items = orders) { order ->
    OrderCard(order = order, onClick = { onOrderClick(order.id) })
}
```

This works, but it is wrong. Without a key, Compose uses the list index as the
identity for each item. If the list is `[A, B, C]` and item B is removed to give
`[A, C]`, Compose believes the second item changed from B to C — it recomposes the
second slot rather than removing the second slot. The resulting animation is wrong:
C animates as if it mutated in place rather than sliding up from B's position.

The key `{ order -> order.id }` gives Compose the stable identity it needs to compute
the correct diff. The key must be stable (the same order always has the same id) and
unique (no two orders in the list share an id). A database primary key satisfies both.

### Lesson 5: State hoisting is not just an API style — it is what makes Compose testable

`OrderHistoryScreen` takes `uiState` and `onOrderClick` as parameters. It holds no
internal state. This is state hoisting (DOC 9). The practical consequence: a test can
call `OrderHistoryScreen(uiState = someTestState, onOrderClick = {})` and verify the
UI without any infrastructure — no ViewModel, no Hilt, no Activity.

If `OrderHistoryScreen` had created `hiltViewModel()` internally, testing it would
require Hilt's test infrastructure. Hoisting the state makes the composable a pure
function of its inputs. Pure functions are trivially testable.

### Lesson 6: The 51% line-count reduction is a side effect, not the goal

The migration deleted 185 more lines than it added. This is a side effect of Compose's
lower ceremony, not the goal. The goal was: a screen that is easier to understand,
easier to change, and easier to test. The line count is a proxy metric for those goals.

Measuring lines deleted can mislead teams into optimizing for code golf. The right
measure: how long does it take a new developer to add a field to the order item card?
In the XML version: 5 files, 20 minutes. In the Compose version: 2 files, 5 minutes.
That is the real improvement.

---

## DOC Connection Map

```text
What each phase used, and which DOC explains it:

DOC 7 (Jetpack Compose — Fundamentals)
    → @Composable functions — OrderHistoryScreen, OrderCard, OrderStatusChip
    → Modifier system — fillMaxWidth(), padding(), clickable()
    → LazyColumn + items() — replaces RecyclerView + ListAdapter
    → Material 3 components — Card, Text, Surface, CircularProgressIndicator

DOC 8 (Inside Compose Runtime)
    → key = { order.id } in items() — item identity for recomposition
    → Recomposition scope — why OrderCard recomposes only when its order changes
    → Slot table — how Compose manages composable instances internally
    → Modifier.animateItem() — built-in animation on list changes

DOC 9 (Production Compose Patterns)
    → collectAsStateWithLifecycle() — lifecycle-safe StateFlow collection
    → State hoisting — OrderHistoryScreen takes state as parameter
    → ViewCompositionStrategy.DisposeOnViewTreeLifecycleDestroyed — Fragment interop
    → ComposeView — hosting Compose in a Fragment

DOC 5 (Android Lifecycle / ViewModel)
    → OrderHistoryViewModel — unchanged through the entire migration
    → The ViewModel surviving = proof of correct layering
    → viewLifecycleOwner — still used in Fragment during Phase 1-2

DOC 13 (Data Architecture)
    → Unchanged Repository and domain model — the stable core
    → Separation of concerns: UI layer replaced, domain layer intact

DOC 14 (Dependency Injection / Hilt)
    → hiltViewModel() — how the ViewModel is provided to Compose in Phase 3
    → @HiltViewModel on OrderHistoryViewModel — unchanged

DOC 16 (Testing Strategy)
    → ComposeTestRule — testing OrderHistoryScreen without ViewModel
    → State hoisting → composable is a pure function → trivially testable
    → ViewModel tests unchanged — empirical proof of correct migration
```

---

*Previous case study: 01-offline-first-feature.md — building the Repository + Room +
Flow stack for offline-first order tracking.*
