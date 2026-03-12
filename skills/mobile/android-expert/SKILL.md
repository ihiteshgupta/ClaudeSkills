---
name: android-expert
description: Expert in Android development with Kotlin, Jetpack Compose, Material 3, Coroutines, and modern Android architecture. Use for building Android apps, widgets, and Google platform integrations.
allowed-tools: Read, Write, Edit, Grep, Glob, Bash
---

# Android Development Expert

## Purpose
Provide expert-level Android development assistance focusing on Kotlin, Jetpack Compose, modern Android architecture, performance optimization, and Google platform best practices.

## When to Use This Skill
- Building Android apps with Jetpack Compose or XML layouts
- Implementing navigation, state management, and data flow
- Working with Room, DataStore, or other persistence solutions
- Integrating Google services (Maps, Firebase, Play Billing, etc.)
- Performance optimization and memory management
- Play Store submission and distribution
- Writing unit and instrumentation tests
- Building widgets, Wear OS, or Android TV apps

## Key Principles

### 1. Modern Kotlin Patterns
- Prefer Jetpack Compose for new UI, XML/Views for legacy maintenance
- Use Kotlin Coroutines and Flow for all async operations
- Leverage sealed classes, data classes, and value classes
- Apply extension functions and scope functions idiomatically
- Use Kotlin DSLs for Gradle configuration

### 2. Jetpack Compose Best Practices
- Keep composables small, focused, and stateless where possible
- Hoist state to the caller — follow unidirectional data flow
- Use remember and derivedStateOf to minimize recompositions
- Implement proper theming with Material 3 (MaterialTheme)
- Use CompositionLocal sparingly — prefer explicit parameter passing
- Provide @Preview functions for rapid iteration

### 3. XML/View System Best Practices
- Use ViewBinding over findViewById or synthetic accessors
- RecyclerView with ListAdapter and DiffUtil
- ConstraintLayout for complex layouts, avoid deep nesting
- Fragment-based navigation with Navigation component
- Use View.GONE/INVISIBLE appropriately

### 4. Architecture Patterns
- MVVM with ViewModel + StateFlow as default
- Clean Architecture for large-scale apps (data/domain/presentation layers)
- Repository pattern for data access abstraction
- Use cases for business logic encapsulation
- Dependency injection with Hilt (preferred) or Koin

### 5. Concurrency & Networking
- Kotlin Coroutines for all async work
- Flow and StateFlow for reactive data streams
- viewModelScope for ViewModel coroutines
- lifecycleScope for lifecycle-aware coroutines
- Retrofit + OkHttp for networking
- Proper error handling with Result or sealed classes

## Code Structure

### Compose Screen Template
```kotlin
@Composable
fun ItemListScreen(
    viewModel: ItemListViewModel = hiltViewModel(),
    onItemClick: (String) -> Unit,
) {
    val uiState by viewModel.uiState.collectAsStateWithLifecycle()

    ItemListContent(
        uiState = uiState,
        onItemClick = onItemClick,
        onRefresh = viewModel::refresh,
        onRetry = viewModel::retry,
    )
}

@Composable
private fun ItemListContent(
    uiState: ItemListUiState,
    onItemClick: (String) -> Unit,
    onRefresh: () -> Unit,
    onRetry: () -> Unit,
) {
    Scaffold(
        topBar = {
            TopAppBar(title = { Text("Items") })
        },
    ) { padding ->
        when (uiState) {
            is ItemListUiState.Loading -> {
                Box(Modifier.fillMaxSize(), contentAlignment = Alignment.Center) {
                    CircularProgressIndicator()
                }
            }
            is ItemListUiState.Success -> {
                LazyColumn(contentPadding = padding) {
                    items(uiState.items, key = { it.id }) { item ->
                        ItemRow(
                            item = item,
                            onClick = { onItemClick(item.id) },
                        )
                    }
                }
            }
            is ItemListUiState.Error -> {
                ErrorContent(
                    message = uiState.message,
                    onRetry = onRetry,
                )
            }
        }
    }
}

@Preview(showBackground = true)
@Composable
private fun ItemListContentPreview() {
    AppTheme {
        ItemListContent(
            uiState = ItemListUiState.Success(sampleItems),
            onItemClick = {},
            onRefresh = {},
            onRetry = {},
        )
    }
}
```

### ViewModel Template
```kotlin
@HiltViewModel
class ItemListViewModel @Inject constructor(
    private val repository: ItemRepository,
) : ViewModel() {

    private val _uiState = MutableStateFlow<ItemListUiState>(ItemListUiState.Loading)
    val uiState: StateFlow<ItemListUiState> = _uiState.asStateFlow()

    init {
        loadItems()
    }

    fun refresh() = loadItems()

    fun retry() = loadItems()

    private fun loadItems() {
        viewModelScope.launch {
            _uiState.value = ItemListUiState.Loading
            repository.getItems()
                .onSuccess { items ->
                    _uiState.value = ItemListUiState.Success(items)
                }
                .onFailure { error ->
                    _uiState.value = ItemListUiState.Error(
                        error.message ?: "Something went wrong"
                    )
                }
        }
    }
}

sealed interface ItemListUiState {
    data object Loading : ItemListUiState
    data class Success(val items: List<Item>) : ItemListUiState
    data class Error(val message: String) : ItemListUiState
}
```

### Repository Template
```kotlin
interface ItemRepository {
    suspend fun getItems(): Result<List<Item>>
    suspend fun getItem(id: String): Result<Item>
    suspend fun saveItem(item: Item): Result<Unit>
    suspend fun deleteItem(id: String): Result<Unit>
}

class ItemRepositoryImpl @Inject constructor(
    private val api: ItemApi,
    private val dao: ItemDao,
    private val dispatcher: CoroutineDispatcher = Dispatchers.IO,
) : ItemRepository {

    override suspend fun getItems(): Result<Item> = withContext(dispatcher) {
        runCatching {
            val remote = api.getItems()
            dao.insertAll(remote.map { it.toEntity() })
            dao.getAll().map { it.toDomain() }
        }.recoverCatching {
            // Fallback to cache on network failure
            dao.getAll().map { it.toDomain() }
        }
    }
}
```

### Networking with Retrofit
```kotlin
interface ItemApi {
    @GET("items")
    suspend fun getItems(): List<ItemDto>

    @GET("items/{id}")
    suspend fun getItem(@Path("id") id: String): ItemDto

    @POST("items")
    suspend fun createItem(@Body item: CreateItemRequest): ItemDto
}

@Module
@InstallIn(SingletonComponent::class)
object NetworkModule {
    @Provides
    @Singleton
    fun provideOkHttpClient(): OkHttpClient =
        OkHttpClient.Builder()
            .addInterceptor(HttpLoggingInterceptor().apply {
                level = HttpLoggingInterceptor.Level.BODY
            })
            .connectTimeout(30, TimeUnit.SECONDS)
            .readTimeout(30, TimeUnit.SECONDS)
            .build()

    @Provides
    @Singleton
    fun provideRetrofit(client: OkHttpClient): Retrofit =
        Retrofit.Builder()
            .baseUrl(BuildConfig.BASE_URL)
            .client(client)
            .addConverterFactory(GsonConverterFactory.create())
            .build()

    @Provides
    @Singleton
    fun provideItemApi(retrofit: Retrofit): ItemApi =
        retrofit.create(ItemApi::class.java)
}
```

## Common Patterns

### 1. Room Database
```kotlin
@Entity(tableName = "items")
data class ItemEntity(
    @PrimaryKey val id: String,
    val title: String,
    val description: String,
    @ColumnInfo(name = "created_at") val createdAt: Long,
)

@Dao
interface ItemDao {
    @Query("SELECT * FROM items ORDER BY created_at DESC")
    suspend fun getAll(): List<ItemEntity>

    @Query("SELECT * FROM items WHERE id = :id")
    suspend fun getById(id: String): ItemEntity?

    @Query("SELECT * FROM items ORDER BY created_at DESC")
    fun observeAll(): Flow<List<ItemEntity>>

    @Upsert
    suspend fun insertAll(items: List<ItemEntity>)

    @Delete
    suspend fun delete(item: ItemEntity)
}

@Database(entities = [ItemEntity::class], version = 1)
abstract class AppDatabase : RoomDatabase() {
    abstract fun itemDao(): ItemDao
}
```

### 2. Navigation with Compose
```kotlin
@Serializable data object Home
@Serializable data class Detail(val itemId: String)
@Serializable data object Settings

@Composable
fun AppNavHost(navController: NavHostController = rememberNavController()) {
    NavHost(navController = navController, startDestination = Home) {
        composable<Home> {
            ItemListScreen(
                onItemClick = { id -> navController.navigate(Detail(id)) },
            )
        }
        composable<Detail> { backStackEntry ->
            val detail: Detail = backStackEntry.toRoute()
            ItemDetailScreen(itemId = detail.itemId)
        }
        composable<Settings> {
            SettingsScreen()
        }
    }
}
```

### 3. Hilt Dependency Injection
```kotlin
@Module
@InstallIn(SingletonComponent::class)
abstract class RepositoryModule {
    @Binds
    @Singleton
    abstract fun bindItemRepository(impl: ItemRepositoryImpl): ItemRepository
}

@Module
@InstallIn(SingletonComponent::class)
object DatabaseModule {
    @Provides
    @Singleton
    fun provideDatabase(@ApplicationContext context: Context): AppDatabase =
        Room.databaseBuilder(context, AppDatabase::class.java, "app.db")
            .fallbackToDestructiveMigration()
            .build()

    @Provides
    fun provideItemDao(db: AppDatabase): ItemDao = db.itemDao()
}
```

### 4. DataStore for Preferences
```kotlin
val Context.dataStore by preferencesDataStore(name = "settings")

object PreferencesKeys {
    val DARK_MODE = booleanPreferencesKey("dark_mode")
    val AUTH_TOKEN = stringPreferencesKey("auth_token")
    val ONBOARDING_COMPLETE = booleanPreferencesKey("onboarding_complete")
}

class SettingsRepository @Inject constructor(
    @ApplicationContext private val context: Context,
) {
    val darkMode: Flow<Boolean> = context.dataStore.data
        .map { it[PreferencesKeys.DARK_MODE] ?: false }

    suspend fun setDarkMode(enabled: Boolean) {
        context.dataStore.edit { it[PreferencesKeys.DARK_MODE] = enabled }
    }
}
```

## Best Practices

1. **Project Organization**
   - Feature-based package structure over layer-based
   - Separate UI, domain, and data layers per feature
   - Use convention plugins for Gradle build logic
   - Multi-module architecture for large apps

2. **Performance**
   - Profile with Android Studio Profiler before optimizing
   - Use key parameter in LazyColumn/LazyRow items
   - Avoid unnecessary recompositions — use stable types
   - Use Coil or Glide for image loading with caching
   - Avoid blocking the main thread — use Dispatchers.IO for I/O
   - Use baseline profiles for startup optimization

3. **Testing**
   - Unit test ViewModels, repositories, and use cases
   - Use fakes over mocks where possible
   - Instrumentation tests for database and UI
   - Use Compose testing APIs (composeTestRule)
   - Test on multiple API levels and screen sizes

4. **Accessibility**
   - Provide contentDescription for images and icons
   - Support TalkBack navigation
   - Use semantics modifiers in Compose
   - Test with Accessibility Scanner
   - Support font scaling and display size changes

5. **App Lifecycle**
   - Handle configuration changes properly
   - Save and restore UI state with SavedStateHandle
   - Handle process death gracefully
   - Manage background work with WorkManager
   - Implement proper deep linking

## Anti-Patterns to Avoid

- Business logic in composables or Activities/Fragments
- Collecting flows without lifecycle awareness (use collectAsStateWithLifecycle)
- Using GlobalScope for coroutines
- Hardcoded strings — use string resources for localization
- Deep composable nesting without extraction
- Using mutableStateOf when StateFlow is more appropriate in ViewModels
- Ignoring ProGuard/R8 rules for release builds
- Not handling back navigation properly

## Tools & Libraries

- **UI**: Jetpack Compose, Material 3, Accompanist
- **Persistence**: Room, DataStore, SQLDelight
- **Networking**: Retrofit, OkHttp, Ktor Client
- **DI**: Hilt, Koin
- **Images**: Coil, Glide
- **Navigation**: Navigation Compose, Voyager
- **Async**: Kotlin Coroutines, Flow
- **Testing**: JUnit, Turbine, Mockk, Compose Test
- **Build**: Gradle Kotlin DSL, Version Catalogs
- **CI/CD**: GitHub Actions, Bitrise, Firebase App Distribution
- **Analytics**: Firebase Analytics, Mixpanel
- **Crash Reporting**: Firebase Crashlytics

## Implementation Approach

When implementing Android features:

1. **Analyze Requirements** — Understand the feature scope and Android platform capabilities
2. **Design Architecture** — Plan data flow, navigation, and module structure
3. **Define Models** — Create data models, DTOs, entities, and domain objects
4. **Implement Data Layer** — Build API clients, DAOs, and repositories
5. **Build ViewModel** — Implement business logic with proper state management
6. **Compose UI** — Build composables following unidirectional data flow
7. **Handle Edge Cases** — Empty states, errors, loading, offline, process death
8. **Test** — Unit test logic, compose test UI, instrumentation test integration
9. **Optimize** — Profile performance, reduce APK size, add baseline profiles

This skill ensures production-ready, maintainable, and performant Android applications following Google's latest guidelines and modern Kotlin patterns.
