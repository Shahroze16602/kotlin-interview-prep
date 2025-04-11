## 4. Jetpack Compose Core Concepts

Jetpack Compose is Android's modern toolkit for building native UI. It simplifies and accelerates UI development on Android with less code, powerful tools, and intuitive Kotlin APIs.

### 4.1 Composables

Composables are the building blocks of Jetpack Compose UI. They are functions annotated with `@Composable` that describe the UI.


```kotlin
@Composable
fun Greeting(name: String) {
    Text(text = "Hello, $name!")
}
```


### 4.2 Recomposition

Recomposition is the process of calling your composable functions again when inputs change. Compose handles this efficiently, updating only the parts of the UI that need to change.

### 4.3 Layouts

Compose provides layout composables to arrange UI elements:

- **`Column`**: Arranges children vertically.

- **`Row`**: Arranges children horizontally.

- **`Box`**: Stacks children on top of each other.


```kotlin
Column {
    Text("Item 1")
    Text("Item 2")
}
```


### 4.4 Modifiers

Modifiers are used to decorate or add behavior to composables. They can change layout, add click listeners, and more.


```kotlin
Text(
    text = "Hello",
    modifier = Modifier
        .padding(16.dp)
        .background(Color.Blue)
)
```


### 4.5 State Management

Managing state is crucial in Compose. You can use `remember` and `mutableStateOf` to hold state within composables.


```kotlin
@Composable
fun Counter() {
    var count by remember { mutableStateOf(0) }
    Button(onClick = { count++ }) {
        Text("Count: $count")
    }
}
```

### 4.6 What is State?

In Jetpack Compose, *state* refers to any data that can change over time and affects what is displayed on the screen. When the state changes, Compose automatically recomposes the affected parts of the UI to reflect the new data. citeturn0search0

---

### 4.7 Local State Management

For managing state within a single composable, you can use `remember` and `mutableStateOf`.

```kotlin
@Composable
fun Counter() {
    var count by remember { mutableStateOf(0) }
    Button(onClick = { count++ }) {
        Text("Clicked $count times")
    }
}
```

- `remember`: Stores the state across recompositions.
- `mutableStateOf`: Creates an observable state holder.

---

### 4.8 State Hoisting

State hoisting is a pattern where you move the state out of a composable to make it stateless. This allows for better reusability and testability.

```kotlin
@Composable
fun Counter(count: Int, onIncrement: () -> Unit) {
    Button(onClick = onIncrement) {
        Text("Clicked $count times")
    }
}

@Composable
fun CounterScreen() {
    var count by remember { mutableStateOf(0) }
    Counter(count = count, onIncrement = { count++ })
}
```


Here, `Counter` is a stateless composable, and `CounterScreen` manages the state.

---

### 4.9 ViewModel and StateFlow

For more complex state management, especially when dealing with asynchronous data or data that needs to survive configuration changes, you can use `ViewModel` in combination with `StateFlow`.

```kotlin
class MyViewModel : ViewModel() {
    private val _uiState = MutableStateFlow(0)
    val uiState: StateFlow<Int> = _uiState

    fun increment() {
        _uiState.value++
    }
}
```


In your composable:

```kotlin
@Composable
fun CounterScreen(viewModel: MyViewModel = viewModel()) {
    val count by viewModel.uiState.collectAsState()
    Counter(count = count, onIncrement = { viewModel.increment() })
}
```


This setup ensures that your UI reacts to state changes managed by the `ViewModel`.

---

### 4.10 Derived State

Sometimes, you need to derive new state based on existing state. Compose provides `derivedStateOf` for this purpose.

```kotlin
val isEven = derivedStateOf { count % 2 == 0 }
```


This ensures that `isEven` is recomputed only when `count` changes.

---

### 4.11 Best Practices

- **Keep UI Stateless**: Whenever possible, keep your UI components stateless and manage state in higher-level components or `ViewModel`.
- **Use StateFlow for Asynchronous Data**: When dealing with data streams or asynchronous operations, prefer `StateFlow` over `LiveData` for better integration with Compose.
- **Avoid Unnecessary Recomposition**: Use `derivedStateOf` and other techniques to prevent unnecessary recompositions, which can impact performance.

---

### 4.12 Example Task

**Task**: Implement a simple login screen with username and password fields. Display a message when both fields are non-empty.

**Steps**:

1. Create a `LoginViewModel` with `MutableStateFlow` for username and password.
2. In your composable, collect the state using `collectAsState()`.
3. Use `TextField` for input and `Text` to display the message when both fields are filled.
