## 3. Coroutines & Concurrency in Kotlin

### 3.1 What Are Coroutines?

Coroutines are lightweight threads provided by Kotlin that allow for asynchronous programming without the complexity of traditional threading. They enable you to write non-blocking, concurrent code in a sequential manner, making it easier to manage tasks like network calls, database operations, and more. citeturn0search0

---

### 3.2 Coroutine Builders

Kotlin provides several coroutine builders to launch coroutines:

- **`launch`**: Starts a new coroutine without blocking the current thread and doesn't return a result. Ideal for tasks that don't require a result.

  
```kotlin
  GlobalScope.launch {
      delay(1000L)
      println("Task from runBlocking")
  }
  ```


- **`async`**: Starts a new coroutine and returns a `Deferred` object, which can be used to retrieve the result with `await()`. Suitable for concurrent tasks that return a result.

  
```kotlin
  val deferred = GlobalScope.async {
      delay(1000L)
      "Result"
  }
  println("Deferred result: ${deferred.await()}")
  ```


- **`runBlocking`**: Bridges the non-coroutine world of a regular `fun main()` and the code with coroutines inside. It blocks the current thread until its body completes.

  
```kotlin
  fun main() = runBlocking {
      launch {
          delay(1000L)
          println("World!")
      }
      println("Hello")
  }
  ```


---

### 3.3 Suspend Functions

A `suspend` function is a function that can be paused and resumed later. These functions can only be called from a coroutine or another suspend function.


```kotlin
suspend fun fetchData(): String {
    delay(1000L) // Simulates a long-running operation
    return "Data fetched"
}
```


---

### 3.4 Coroutine Scopes

Coroutine scopes manage the lifecycle of coroutines. In Android, it's common to use scopes tied to the lifecycle of components to prevent memory leaks:

- **`GlobalScope`**: Global scope for launching top-level coroutines that are operating throughout the whole application lifetime. Use with caution.

- **`CoroutineScope`**: An interface for defining a scope for new coroutines.

- **`lifecycleScope`**: Provided by AndroidX, it's tied to the lifecycle of a `LifecycleOwner` (like an Activity or Fragment).

  
```kotlin
  class MyActivity : AppCompatActivity() {
      override fun onCreate(savedInstanceState: Bundle?) {
          super.onCreate(savedInstanceState)
          lifecycleScope.launch {
              // Coroutine tied to the Activity's lifecycle
          }
      }
  }
  ```


---

### 3.5 Dispatchers

Dispatchers determine the thread on which a coroutine runs:

- **`Dispatchers.Main`**: Runs on the main thread. Used for UI interactions.

- **`Dispatchers.IO`**: Optimized for offloading blocking IO tasks (e.g., file I/O, network operations).

- **`Dispatchers.Default`**: Optimized for CPU-intensive work.

- **`Dispatchers.Unconfined`**: Starts the coroutine in the current call-frame until the first suspension.


```kotlin
lifecycleScope.launch(Dispatchers.IO) {
    val data = fetchData()
    withContext(Dispatchers.Main) {
        // Update UI with data
    }
}
```


---

### 3.6 Structured Concurrency

Structured concurrency ensures that coroutines are not left running in the background, preventing memory leaks. It ties the lifecycle of coroutines to a specific scope.


```kotlin
fun main() = runBlocking {
    launch {
        delay(1000L)
        println("Task 1")
    }
    launch {
        delay(500L)
        println("Task 2")
    }
}
```


In this example, both coroutines are launched within the `runBlocking` scope, ensuring they complete before the program exits.

---

### 3.7 Exception Handling

Exceptions in coroutines can be handled using `try-catch` blocks or a `CoroutineExceptionHandler`:


```kotlin
val handler = CoroutineExceptionHandler { _, exception ->
    println("Caught exception: $exception")
}

GlobalScope.launch(handler) {
    throw IOException("IO error")
}
```


Note: Exceptions in `async` coroutines need to be handled when calling `await()`.

---

### 3.8 Shared Mutable State

When multiple coroutines access shared mutable state, synchronization is necessary to prevent race conditions. Kotlin provides mechanisms like `Mutex` and `AtomicInteger` for this purpose.


```kotlin
val mutex = Mutex()
var counter = 0

coroutineScope {
    repeat(1000) {
        launch {
            mutex.withLock {
                counter++
            }
        }
    }
}
```


---

### 3.9 Channels

Channels are a way to communicate between coroutines. They allow one coroutine to send data to another.


```kotlin
val channel = Channel<Int>()
launch {
    for (x in 1..5) channel.send(x)
    channel.close()
}
for (y in channel) println(y)
```


---
Apologies for the incomplete response earlier. Let's continue and complete the discussion on **Kotlin Flow**, followed by the next topic: **Jetpack Compose Core Concepts**.

---

### 3.10 Flow (Continued)

### Cold vs. Hot Flows

- **Cold Flows**: Start emitting values only when collected. Each collector triggers a new execution.

  
```kotlin
  val coldFlow = flow {
      emit("Hello")
      delay(1000)
      emit("World")
  }
  ```


- **Hot Flows**: Emit values regardless of collectors. Examples include `StateFlow` and `SharedFlow`.

  - **StateFlow**: Holds a single updatable data value and emits updates to collectors. Suitable for representing UI state.

    ```kotlin
    private val _state = MutableStateFlow("Initial")
    val state: StateFlow<String> = _state
    ```

  - **SharedFlow**: A hot flow that allows multiple collectors to receive emitted values. Useful for events like navigation or showing messages.

    ```kotlin
    private val _events = MutableSharedFlow<String>()
    val events: SharedFlow<String> = _events
    ```

### Flow Operators

Kotlin Flow provides a rich set of operators to transform and handle data:

- **`map`**: Transforms each emitted value.

  
```kotlin
  flowOf(1, 2, 3).map { it * 2 }
  ```


- **`filter`**: Filters emitted values based on a predicate.

  
```kotlin
  flowOf(1, 2, 3).filter { it % 2 == 0 }
  ```


- **`catch`**: Handles exceptions in the upstream flow.

  
```kotlin
  flow {
      emit(1)
      throw Exception("Error")
  }.catch { e -> emit(-1) }
  ```


- **`onEach`**: Performs an action on each emitted value.

  
```kotlin
  flowOf(1, 2, 3).onEach { println(it) }
  ```


### Collecting Flows in Jetpack Compose

In Jetpack Compose, you can collect flows using `collectAsState` to observe changes and update the UI accordingly.


```kotlin
@Composable
fun Greeting(viewModel: MyViewModel) {
    val name by viewModel.nameFlow.collectAsState()
    Text(text = "Hello, $name!")
}
```

