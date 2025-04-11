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
