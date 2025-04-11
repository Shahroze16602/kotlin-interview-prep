### 11. Performance Optimization

Optimizing performance is critical to ensure that your Android app runs smoothly, especially when dealing with complex UIs, large datasets, or intensive background operations. In Jetpack Compose, you need to focus on improving the efficiency of UI rendering, managing memory, and optimizing background tasks.

---

### 11.1 UI Performance Optimization

Jetpack Compose is declarative, which makes it easy to build UIs, but this can sometimes lead to performance issues if not managed properly. The main areas for optimization in Compose are **recomposition**, **UI rendering**, and **resource management**.

#### 1. **Minimize Unnecessary Recompositions**

In Compose, **recomposition** occurs when the state of the UI changes. If you don’t manage your state efficiently, it can trigger unnecessary recompositions, leading to poor performance.

- **Use `remember` and `rememberUpdatedState`:** Store values that don’t need to be recomposed with `remember`. This helps in avoiding recomposition when the value doesn’t change.

```kotlin
@Composable
fun Greeting(name: String) {
    val updatedName = rememberUpdatedState(name)
    Text("Hello, ${updatedName.value}")
}
```

- **Optimize State Updates:** Ensure that only the necessary parts of the UI are updated by using more granular state management (e.g., `State`, `MutableState`, `LiveData`, or `StateFlow`).

#### 2. **Use `LaunchedEffect` and `rememberCoroutineScope`**

When performing side effects like launching a coroutine or updating UI state, use `LaunchedEffect` or `rememberCoroutineScope` to avoid unnecessary work or re-running the logic on every recomposition.

```kotlin
@Composable
fun FetchData() {
    val scope = rememberCoroutineScope()

    LaunchedEffect(Unit) {
        scope.launch {
            // Perform network or database operations
        }
    }
}
```

This ensures that the network call is only made when the composable enters the composition and not on every recomposition.

#### 3. **Use `LazyColumn` and `LazyRow` for Long Lists**

When dealing with long lists of data, use `LazyColumn` or `LazyRow` instead of `Column` or `Row`. These composables only render items that are currently visible on the screen, which significantly improves performance.

```kotlin
@Composable
fun UserList(users: List<User>) {
    LazyColumn {
        items(users) { user ->
            Text(text = "Name: ${user.name}")
        }
    }
}
```

This approach improves performance when working with large datasets.

#### 4. **Use `Modifier` Efficiently**

Modifiers can be expensive if overused or used improperly. For example, applying multiple `padding`, `align`, or `fillMaxSize` modifiers to a single view can cause inefficiencies.

- Combine modifiers when possible.

```kotlin
Modifier
    .fillMaxWidth()
    .padding(16.dp)
```

- Avoid unnecessary layout constraints. For instance, avoid using `wrapContent` unless needed for the layout.

---

### 11.2 Memory Management

Memory leaks and inefficient memory usage can slow down the app or cause crashes. It's important to manage resources like bitmaps, large data structures, and background processes efficiently.

#### 1. **Avoid Memory Leaks**

When working with coroutines or any long-running background tasks, make sure you cancel them when the composable is no longer needed or when the lifecycle changes.

- **Use `DisposableEffect` for cleanup:**

```kotlin
@Composable
fun CleanupExample() {
    DisposableEffect(Unit) {
        onDispose {
            // Clean up resources (e.g., cancel coroutines)
        }
    }
}
```

This ensures that any resources are cleaned up when the composable is removed from the composition.

#### 2. **Optimize Image Loading**

If your app loads images from the internet or local storage, ensure that you are efficiently managing memory by using libraries like **Coil** or **Glide**, which automatically handle image loading and caching.

- **Using Coil for image loading:**

```kotlin
Image(painter = rememberImagePainter("https://example.com/image.jpg"), contentDescription = null)
```

Coil takes care of caching and memory management internally.

#### 3. **Manage Large Data Efficiently**

When dealing with large data sets or objects (e.g., in a database), use techniques like **pagination** or **data streaming** to load data in chunks rather than all at once.

---

### 11.3 Background Task Optimization

Background tasks, like network calls or database operations, should be managed efficiently to avoid blocking the UI thread and to conserve battery life.

#### 1. **Use Coroutines for Background Tasks**

Coroutines provide an easy and efficient way to handle asynchronous tasks without blocking the UI thread.

- **Launch long-running tasks in a background thread:**

```kotlin
viewModelScope.launch {
    val data = fetchData() // Perform network operation
    updateUi(data) // Update UI on the main thread
}
```

Make sure to always use `viewModelScope` or another lifecycle-aware scope to prevent memory leaks.

#### 2. **Use WorkManager for Background Tasks**

For tasks that need to run in the background even when the app is not in the foreground, **WorkManager** is a great tool to schedule and manage tasks like syncing data or uploading files.

- **Simple WorkManager Task:**

```kotlin
val workRequest = OneTimeWorkRequestBuilder<MyWorker>()
    .setInputData(workDataOf("key" to "value"))
    .build()

WorkManager.getInstance(context).enqueue(workRequest)
```

#### 3. **Throttle Network Requests**

To reduce unnecessary network calls, consider implementing request throttling or caching mechanisms to avoid redundant work.

- Use **Caching** for frequently accessed data and **Retrofit’s Cache** feature to limit the number of network requests.

---

### 11.4 Profile and Measure Performance

Use Android's built-in tools to monitor and optimize the performance of your app.

#### 1. **Android Profiler (CPU, Memory, Network)**

Android Studio provides the **Profiler** tool to analyze CPU usage, memory usage, and network activity in real-time. You can access it from the **View > Tool Windows > Profiler**.

#### 2. **Compose Metrics**

Jetpack Compose provides performance metrics, which you can enable to understand UI rendering performance:

```kotlin
DebugInspectorInfo.setEnabled(true)
```

This allows you to track rendering performance and optimize composables.

---

### 🧠 Interview Q&A Practice – Performance Optimization

1. **How do you avoid unnecessary recompositions in Jetpack Compose?**
2. **What is the advantage of using `LazyColumn` over `Column` for displaying long lists?**
3. **How do you handle memory management and avoid memory leaks in a Jetpack Compose app?**
4. **What are some techniques you use to optimize the loading of images in your app?**
5. **How do you manage background tasks in Android, especially when the app is in the background?**
6. **Why is it important to use `DisposableEffect` for cleanup in Compose?**
7. **What tools do you use to profile and monitor performance in Android?**

---

### 🔧 Mini Task to Try

**Goal**: Create a list of users that are loaded using a network request. Use `LazyColumn` to display the data. Ensure that:
- You optimize the layout by reducing unnecessary recompositions.
- Handle image loading efficiently using Coil.
- Ensure that long network requests are handled in the background using coroutines.

---

### 🎯 Recap

- Optimized UI rendering by minimizing recompositions and using `LazyColumn` for long lists.
- Managed memory efficiently by avoiding leaks and using proper lifecycle management tools like `DisposableEffect`.
- Optimized background tasks using **WorkManager** and **Coroutines**.
- Profiled app performance using Android's built-in tools.
