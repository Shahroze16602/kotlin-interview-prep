## 7. Navigation in Jetpack Compose

Jetpack Compose offers a modern and declarative way of handling navigation within an app. We'll dive into the essential concepts such as navigation setup, nested navigation, passing arguments, and managing navigation with ViewModels.

### 7.1 Basic Setup for Navigation in Jetpack Compose

To use navigation in Jetpack Compose, we need to include the `Navigation` dependency.

1. **Add the Navigation Dependency**:

In your `build.gradle` (app-level) file:

```groovy
dependencies {
    implementation "androidx.navigation:navigation-compose:2.5.3"
}
```

2. **Basic Navigation Setup**:

To begin with, you need to create a `NavHost` and define composable destinations within the `NavHost`.

```kotlin
@Composable
fun AppNavHost() {
    val navController = rememberNavController()

    NavHost(navController = navController, startDestination = "home") {
        composable("home") { HomeScreen(navController) }
        composable("details/{userId}") { backStackEntry ->
            val userId = backStackEntry.arguments?.getString("userId") ?: ""
            DetailsScreen(userId)
        }
    }
}
```

### 7.2 Navigation with Arguments

In Jetpack Compose, you can pass arguments through the navigation route. This is useful when you want to pass data from one screen to another.

For example, to navigate from the `HomeScreen` to a `DetailsScreen` with a `userId`:

```kotlin
@Composable
fun HomeScreen(navController: NavController) {
    val userId = "123"

    Button(onClick = {
        navController.navigate("details/$userId")
    }) {
        Text("Go to Details")
    }
}

@Composable
fun DetailsScreen(userId: String) {
    Text("User ID: $userId")
}
```

The `{userId}` is a path parameter in the route. We can retrieve this value in the `DetailsScreen` via the `NavBackStackEntry` object.

### 7.3 Nested Navigation

Jetpack Compose also supports nested navigation, allowing you to have different navigation graphs inside a main navigation graph.

Example of a nested navigation graph:

```kotlin
@Composable
fun AppNavHost() {
    val navController = rememberNavController()

    NavHost(navController = navController, startDestination = "home") {
        composable("home") { HomeScreen(navController) }
        
        navigation(startDestination = "details", route = "userNav") {
            composable("details") { DetailsScreen() }
            composable("edit") { EditScreen() }
        }
    }
}
```

In this example, we have a parent navigation graph (`userNav`) and two child screens (`details` and `edit`).

### 7.4 Navigation with ViewModel

Managing the navigation state through a `ViewModel` allows for better state management and lifecycle-aware navigation. For example, you can navigate based on changes to your data that reside in the `ViewModel`.

Example of ViewModel-driven navigation:

```kotlin
class MainViewModel : ViewModel() {
    private val _navigateToDetails = MutableLiveData<String>()
    val navigateToDetails: LiveData<String> = _navigateToDetails

    fun onUserClick(userId: String) {
        _navigateToDetails.value = userId
    }
}

@Composable
fun HomeScreen(viewModel: MainViewModel, navController: NavController) {
    val userId by viewModel.navigateToDetails.observeAsState()

    if (userId != null) {
        navController.navigate("details/$userId")
    }

    Button(onClick = {
        viewModel.onUserClick("123")
    }) {
        Text("Go to Details")
    }
}
```

In this example, the `ViewModel` is responsible for triggering navigation when a user is clicked. The `LiveData` is observed, and once the value is updated, the navigation happens in the Composable function.

### 7.5 Back Stack and Up Navigation

To handle back navigation, you need to properly manage the back stack. In Compose, you can use `NavController`’s `popBackStack` or `navigateUp()` to control the navigation.

1. **Navigate Back**:

You can use the `navigateUp()` function to handle back navigation.

```kotlin
Button(onClick = { navController.navigateUp() }) {
    Text("Go Back")
}
```

2. **Pop Back Stack**:

You can pop back the last screen in the stack with:

```kotlin
navController.popBackStack()
```

### 7.6 Navigation Animation (Optional)

Jetpack Compose also supports navigation transitions between composables. You can specify enter and exit transitions using the `AnimatedContent` API.

Example:

```kotlin
NavHost(
    navController = navController,
    startDestination = "home"
) {
    composable(
        "home",
        enterTransition = { slideInVertically() },
        exitTransition = { slideOutVertically() }
    ) {
        HomeScreen(navController)
    }

    composable("details/{userId}") {
        DetailsScreen()
    }
}
```

This will add a sliding transition when navigating between the screens.

---

### 🧠 Interview Q&A Practice – Navigation in Jetpack Compose

1. **What is the difference between `NavHost` and `NavController` in Jetpack Compose?**
2. **How would you pass data from one screen to another using Jetpack Compose navigation?**
3. **Can you explain how nested navigation works in Jetpack Compose and when you would use it?**
4. **How would you manage the navigation state using a `ViewModel` in Jetpack Compose?**
5. **What is the role of `popBackStack()` and `navigateUp()` in navigation?**
6. **Explain how to handle back navigation with Jetpack Compose and the back stack.**

---

### 🔧 Mini Task to Try

**Goal**: Create a simple two-screen app using Jetpack Compose navigation, where the first screen has a button to navigate to the second screen. The second screen should display a message passed from the first screen.
