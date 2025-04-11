### 9. Networking (Retrofit, Ktor)

Networking is essential for apps that need to fetch data from remote servers or interact with APIs. We'll focus on how to use **Retrofit** and **Ktor**, which are popular libraries for making HTTP requests in Android apps.

---

### 9.1 Retrofit Setup

Retrofit is a type-safe HTTP client for Android and Java, which simplifies API requests and parsing responses.

#### 1. **Add Retrofit Dependencies**

In your `build.gradle` (app-level) file, add the Retrofit dependencies:

```groovy
dependencies {
    implementation "com.squareup.retrofit2:retrofit:2.9.0"
    implementation "com.squareup.retrofit2:converter-gson:2.9.0"
    implementation "com.squareup.okhttp3:logging-interceptor:4.9.0"
}
```

#### 2. **Create a Retrofit Interface**

Define an interface with the endpoints you need to interact with:

```kotlin
interface ApiService {

    @GET("users")
    suspend fun getUsers(): List<User>

    @GET("users/{id}")
    suspend fun getUser(@Path("id") userId: String): User
}
```

- `@GET` indicates a GET request.
- `@Path` is used to replace dynamic values in the URL.
- We use `suspend` functions to make these calls asynchronous in Kotlin.

#### 3. **Create a Retrofit Instance**

Set up a Retrofit instance with the base URL and the Gson converter to handle JSON parsing:

```kotlin
object RetrofitInstance {

    private const val BASE_URL = "https://jsonplaceholder.typicode.com/"

    val api: ApiService by lazy {
        Retrofit.Builder()
            .baseUrl(BASE_URL)
            .addConverterFactory(GsonConverterFactory.create())
            .client(OkHttpClient.Builder().addInterceptor(HttpLoggingInterceptor().apply {
                level = HttpLoggingInterceptor.Level.BODY
            }).build())
            .build()
            .create(ApiService::class.java)
    }
}
```

This instance will be used to make network requests to the API.

#### 4. **Calling Retrofit API in ViewModel**

In your `ViewModel`, you can call the API using the `suspend` functions from the Retrofit interface:

```kotlin
@HiltViewModel
class UserViewModel @Inject constructor() : ViewModel() {

    private val _users = mutableStateOf<List<User>>(emptyList())
    val users: State<List<User>> = _users

    init {
        viewModelScope.launch {
            try {
                _users.value = RetrofitInstance.api.getUsers()
            } catch (e: Exception) {
                // Handle error
            }
        }
    }
}
```

Here, we call the `getUsers()` function from the `ApiService` interface inside a `launch` block to ensure it runs asynchronously.

#### 5. **Display Data in Composables**

You can now observe the state of the users and display them in your Composables:

```kotlin
@Composable
fun UserListScreen(viewModel: UserViewModel = hiltViewModel()) {
    val users = viewModel.users.value

    LazyColumn {
        items(users) { user ->
            Text(text = "Name: ${user.name}, Email: ${user.email}")
        }
    }
}
```

---

### 9.2 Ktor Setup

Ktor is a lightweight and flexible HTTP client for making network requests. It’s often used for Kotlin-based projects, especially in Android.

#### 1. **Add Ktor Dependencies**

In your `build.gradle` (app-level) file, add Ktor dependencies:

```groovy
dependencies {
    implementation "io.ktor:ktor-client-core:2.1.0"
    implementation "io.ktor:ktor-client-cio:2.1.0"
    implementation "io.ktor:ktor-client-json:2.1.0"
    implementation "io.ktor:ktor-client-serialization:2.1.0"
}
```

#### 2. **Set Up Ktor Client**

You need to configure the Ktor client instance:

```kotlin
object KtorClient {

    private val client = HttpClient(CIO) {
        install(JsonFeature) {
            serializer = KotlinxSerializer(Json { ignoreUnknownKeys = true })
        }
    }

    suspend fun getUsers(): List<User> {
        return client.get("https://jsonplaceholder.typicode.com/users")
    }
}
```

This example uses the `CIO` engine and the `JsonFeature` for JSON parsing.

#### 3. **Using Ktor in ViewModel**

Now, in your `ViewModel`, you can make network requests with Ktor:

```kotlin
@HiltViewModel
class UserViewModel @Inject constructor() : ViewModel() {

    private val _users = mutableStateOf<List<User>>(emptyList())
    val users: State<List<User>> = _users

    init {
        viewModelScope.launch {
            try {
                _users.value = KtorClient.getUsers()
            } catch (e: Exception) {
                // Handle error
            }
        }
    }
}
```

#### 4. **Display Data in Composables (Same as Retrofit)**

You can display the data using the same `UserListScreen` composable we used in the Retrofit example.

---

### 9.3 Error Handling and Network State

When dealing with network requests, you must handle errors properly to ensure your app remains responsive.

1. **Show Loading State**:

You can show a loading state while data is being fetched:

```kotlin
@Composable
fun UserListScreen(viewModel: UserViewModel = hiltViewModel()) {
    val users = viewModel.users.value
    val isLoading = users.isEmpty()

    if (isLoading) {
        CircularProgressIndicator()
    } else {
        LazyColumn {
            items(users) { user ->
                Text(text = "Name: ${user.name}, Email: ${user.email}")
            }
        }
    }
}
```

2. **Handle Errors Gracefully**:

You can display an error message if something goes wrong during the network request:

```kotlin
@Composable
fun UserListScreen(viewModel: UserViewModel = hiltViewModel()) {
    val users = viewModel.users.value
    val isLoading = users.isEmpty()
    val errorMessage = remember { mutableStateOf<String?>(null) }

    if (isLoading) {
        CircularProgressIndicator()
    } else if (errorMessage.value != null) {
        Text(text = "Error: ${errorMessage.value}")
    } else {
        LazyColumn {
            items(users) { user ->
                Text(text = "Name: ${user.name}, Email: ${user.email}")
            }
        }
    }
}
```

---

### 🧠 Interview Q&A Practice – Networking

1. **What is the main difference between Retrofit and Ktor in terms of usage in Android?**
2. **How do you handle network errors in a clean way?**
3. **What are the benefits of using Retrofit’s `GsonConverterFactory`?**
4. **How would you manage authentication (e.g., JWT tokens) in Retrofit or Ktor?**
5. **How would you handle pagination or large datasets with Retrofit or Ktor in a Compose app?**
6. **What is the purpose of `HttpLoggingInterceptor` in Retrofit?**

---

### 🔧 Mini Task to Try

**Goal**: Create a simple app where you fetch a list of posts from an API using Retrofit or Ktor and display them in a `LazyColumn`. The app should also handle loading and error states.

---

### 🎯 Recap

- Set up **Retrofit** for network requests with a type-safe API.
- Set up **Ktor** for network requests with a lightweight client.
- Handled loading, error, and data states in Jetpack Compose.
- Practiced how to display data fetched from a remote API in a UI.
