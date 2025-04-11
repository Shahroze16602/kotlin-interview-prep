## 5. Architecture in Android: MVVM & Clean Architecture

### 5.1 MVVM (Model-View-ViewModel)

MVVM is a design pattern that promotes a clear separation of concerns, making your codebase more modular and testable. It's widely adopted in Android development, especially with Jetpack components.

#### Components:

- **Model**: Represents the data and business logic. It fetches data from repositories or remote sources.

- **View**: The UI layer (Activities, Fragments, or Composables) that displays data and captures user interactions.

- **ViewModel**: Acts as a bridge between the Model and the View. It holds UI-related data and handles logic to prepare data for the UI.

#### Example:

```kotlin
// ViewModel
class UserViewModel : ViewModel() {
    private val _user = MutableLiveData<User>()
    val user: LiveData<User> = _user

    fun fetchUser() {
        // Fetch user data from repository
    }
}

// View (Composable)
@Composable
fun UserProfile(viewModel: UserViewModel = viewModel()) {
    val user by viewModel.user.observeAsState()
    // Display user data
}
```


In this setup, the `ViewModel` fetches and holds the user data, which the `View` observes and displays. This separation allows for easier testing and maintenance.

---

### 5.2 Clean Architecture

Clean Architecture, introduced by Robert C. Martin (Uncle Bob), emphasizes the separation of concerns and independence of frameworks, UI, and databases. It structures the codebase into layers, each with distinct responsibilities.

#### Layers:

1. **Entities**: Enterprise-wide business rules. They are the most general and high-level rules.

2. **Use Cases (Interactors)**: Application-specific business rules. They orchestrate the flow of data to and from the entities.

3. **Interface Adapters**: Convert data from the format most convenient for the use cases and entities to the format most convenient for external agencies like databases and the web.

4. **Frameworks & Drivers**: External agents like UI, databases, and frameworks.

The key principle is that dependencies can only point inwards; outer layers can depend on inner layers, but not vice versa.

#### Example:

In an Android app:

- **Entities**: Plain Kotlin classes representing core business objects.

- **Use Cases**: Classes that implement business logic, using entities and interfaces defined in the domain layer.

- **Interface Adapters**: ViewModels and Repositories that adapt data from the domain layer to the presentation layer.

- **Frameworks & Drivers**: Android-specific components like Activities, Fragments, and Jetpack libraries.

This architecture ensures that the core business logic remains unaffected by changes in external layers, promoting testability and scalability.

---

### 5.3 Combining MVVM with Clean Architecture

In practice, MVVM can be integrated within the Clean Architecture framework:

- **View**: UI components (Activities, Fragments, Composables).

- **ViewModel**: Part of the Interface Adapters layer, interacting with Use Cases.

- **Use Cases**: Encapsulate business logic, interacting with Entities and Repositories.

- **Repositories**: Provide data to Use Cases, fetching from data sources.

- **Data Sources**: Implementations that fetch data from local or remote sources.

This combination leverages the strengths of both patterns, resulting in a robust and maintainable architecture.

---

### 5.4 Best Practices

- **Separation of Concerns**: Ensure each layer has a single responsibility.

- **Dependency Inversion**: Depend on abstractions, not concrete implementations.

- **Testability**: Design components to be easily testable in isolation.

- **Scalability**: Structure the codebase to accommodate future growth and changes.

---

### 5.5 Sample Task

**Task**: Implement a feature to fetch and display a list of articles.

**Steps**:

1. **Entities**: Define an `Article` data class.

2. **Use Case**: Create a `GetArticlesUseCase` that fetches articles from a repository.

3. **Repository Interface**: Define an `ArticleRepository` interface in the domain layer.

4. **Repository Implementation**: Implement `ArticleRepositoryImpl` in the data layer, fetching data from a remote source.

5. **ViewModel**: Create an `ArticleViewModel` that uses `GetArticlesUseCase` to fetch data.

6. **View**: Implement a Composable that observes the `ArticleViewModel` and displays the list of articles.

This exercise reinforces the application of Clean Architecture principles in a real-world scenario.
