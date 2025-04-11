## 6. Dependency Injection (DI) in Android using Hilt

### 6.1 What is Dependency Injection?

Dependency Injection is a design pattern that allows a class to receive its dependencies from external sources rather than creating them itself. This promotes loose coupling, making the codebase more modular, testable, and maintainable.

---

### 6.2 Why Use Hilt?

Hilt is a dependency injection library built on top of Dagger, designed specifically for Android. It offers:

- **Simplified DI setup**: Reduces boilerplate code.
- **Integrated Android support**: Works seamlessly with Android components like Activities, Fragments, ViewModels, etc.
- **Lifecycle-aware components**: Manages the lifecycle of dependencies automatically.
- **Improved testability**: Facilitates easier testing by allowing dependencies to be mocked or replaced.

---

### 6.3 Setting Up Hilt in Your Project

1. **Add Hilt dependencies**:

   In your project-level `build.gradle`:

   ```groovy
   plugins {
       id 'com.google.dagger.hilt.android' version '2.51.1' apply false
   }
   ```


   In your app-level `build.gradle`:

   ```groovy
   plugins {
       id 'com.android.application'
       id 'kotlin-kapt'
       id 'com.google.dagger.hilt.android'
   }

   dependencies {
       implementation "com.google.dagger:hilt-android:2.51.1"
       kapt "com.google.dagger:hilt-compiler:2.51.1"
   }
   ```


2. **Enable Java 8 features**:

   ```groovy
   android {
       compileOptions {
           sourceCompatibility JavaVersion.VERSION_1_8
           targetCompatibility JavaVersion.VERSION_1_8
       }
   }
   ```


3. **Initialize Hilt in your Application class**:

   ```kotlin
   @HiltAndroidApp
   class MyApplication : Application()
   ```


---

### 6.4 Injecting Dependencies

**Constructor Injection**:

Use `@Inject` to annotate the constructor of the class you want to inject.


```kotlin
class AnalyticsService @Inject constructor()
```


**Field Injection**:

Annotate Android components with `@AndroidEntryPoint` and use `@Inject` to request dependencies.


```kotlin
@AndroidEntryPoint
class MainActivity : AppCompatActivity() {

    @Inject lateinit var analyticsService: AnalyticsService
}
```


---

### 6.5 Providing Dependencies with Modules

Use `@Module` and `@InstallIn` to define how to provide instances of classes that you don't own or can't annotate with `@Inject`.


```kotlin
@Module
@InstallIn(SingletonComponent::class)
object NetworkModule {

    @Provides
    fun provideAnalyticsService(): AnalyticsService {
        return AnalyticsServiceImpl()
    }
}
```


---

### 6.6 Scoping Dependencies

Hilt provides predefined scopes to manage the lifecycle of dependencies:

- `@Singleton`: Application-wide singleton.
- `@ActivityRetainedScoped`: Retained across configuration changes.
- `@ViewModelScoped`: Scoped to a ViewModel's lifecycle.
- `@ActivityScoped`: Scoped to an Activity's lifecycle.
- `@FragmentScoped`: Scoped to a Fragment's lifecycle.

Example:


```kotlin
@Module
@InstallIn(SingletonComponent::class)
object AppModule {

    @Provides
    @Singleton
    fun provideRepository(): Repository {
        return RepositoryImpl()
    }
}
```


---

### 6.7 Injecting into ViewModels

Hilt integrates with Jetpack's ViewModel by using `@HiltViewModel`.


```kotlin
@HiltViewModel
class MainViewModel @Inject constructor(
    private val repository: Repository
) : ViewModel()
```


In your Composable:


```kotlin
@Composable
fun MainScreen(viewModel: MainViewModel = hiltViewModel()) {
    // Use viewModel
}
```


---

### 6.8 Best Practices

- **Use constructor injection**: Prefer constructor injection over field injection for better testability.
- **Scope appropriately**: Assign correct scopes to your dependencies to manage their lifecycles effectively.
- **Modularize your code**: Organize your modules logically, e.g., NetworkModule, DatabaseModule.
- **Avoid unnecessary singletons**: Only use `@Singleton` when a single instance is truly needed.
- **Leverage interfaces**: Program to interfaces rather than concrete implementations for flexibility.

---

### 6.9 Sample Task

**Objective**: Implement a feature to fetch and display a list of users using Hilt for dependency injection.

**Steps**:

1. **Create a data model**:

   ```kotlin
   data class User(val id: Int, val name: String)
   ```


2. **Define a Repository interface**:

   ```kotlin
   interface UserRepository {
       suspend fun getUsers(): List<User>
   }
   ```


3. **Implement the Repository**:

   ```kotlin
   class UserRepositoryImpl @Inject constructor(): UserRepository {
       override suspend fun getUsers(): List<User> {
           // Fetch users from a data source
       }
   }
   ```

4. **Provide the Repository with Hilt Module**:

```kotlin
@Module
@InstallIn(SingletonComponent::class)
object RepositoryModule {

    @Provides
    @Singleton
    fun provideUserRepository(): UserRepository {
        return UserRepositoryImpl()
    }
}
```

5. **Create the ViewModel and inject the repository**:

```kotlin
@HiltViewModel
class UserViewModel @Inject constructor(
    private val repository: UserRepository
) : ViewModel() {

    var users by mutableStateOf<List<User>>(emptyList())
        private set

    init {
        viewModelScope.launch {
            users = repository.getUsers()
        }
    }
}
```

6. **Create the Composable UI**:

```kotlin
@Composable
fun UserListScreen(viewModel: UserViewModel = hiltViewModel()) {
    val users = viewModel.users

    LazyColumn {
        items(users) { user ->
            Text(text = user.name, modifier = Modifier.padding(16.dp))
        }
    }
}
```

7. **Inject the dependencies into the Activity**:

```kotlin
@AndroidEntryPoint
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            UserListScreen()
        }
    }
}
```

---

### 🔁 Recap

- Used `@HiltViewModel` for the ViewModel.
- Injected `UserRepository` into ViewModel.
- Defined the repository provision in a Hilt `@Module`.
- Used `@AndroidEntryPoint` to enable injection into `MainActivity`.

---

### 🧠 Interview Q&A Practice – Dependency Injection with Hilt

1. **What is the difference between constructor and field injection? When would you use each?**

2. **Explain the purpose of `@HiltViewModel`. How does it help in lifecycle management?**

3. **How does Hilt differ from Dagger? Why would you use Hilt in an Android project?**

4. **What is the significance of scoping in Hilt? What happens if you don't scope your dependencies properly?**

5. **What are the benefits of modularizing your Hilt modules?**

---

### 🔧 Mini Task to Try

**Goal**: Add a network layer and inject Retrofit using Hilt

- Define a Retrofit instance provider in a `NetworkModule`
- Create a `UserApi` interface
- Inject `UserApi` into `UserRepositoryImpl`
- Update `getUsers()` to call the API
