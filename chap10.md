### 10. Testing (Unit/UI Testing in Compose)

Testing is crucial to ensure that your app works as expected, especially when working with complex UIs or data operations. Jetpack Compose supports testing through its own testing library, and there are also tools for unit testing your ViewModels, network calls, and repositories.

---

### 10.1 Unit Testing in Kotlin

Unit tests are used to test small units of functionality (like individual functions or classes) in isolation. In Android, unit tests are typically written using JUnit and Mockito for mocking dependencies.

#### 1. **Add Testing Dependencies**

In your `build.gradle` (app-level) file, include the necessary dependencies for testing:

```groovy
dependencies {
    testImplementation "junit:junit:4.13.2"
    testImplementation "org.mockito:mockito-core:4.6.1"
    testImplementation "org.mockito:mockito-inline:4.6.1"
    testImplementation "androidx.arch.core:core-testing:2.1.0"
    testImplementation "org.jetbrains.kotlinx:kotlinx-coroutines-test:1.6.0"
}
```

#### 2. **Mocking Dependencies with Mockito**

To mock dependencies in unit tests (like a repository or network client), you can use **Mockito**:

```kotlin
class UserRepositoryTest {

    private lateinit var userRepository: UserRepository
    private lateinit var userDao: UserDao

    @Before
    fun setup() {
        userDao = mock(UserDao::class.java)
        userRepository = UserRepository(userDao)
    }

    @Test
    fun testGetUsers() = runTest {
        val fakeUsers = listOf(User(1, "John Doe", "john@example.com"))
        whenever(userDao.getUsers()).thenReturn(fakeUsers)

        val result = userRepository.getUsers()

        assertEquals(fakeUsers, result)
    }
}
```

- In the example, we mock the `UserDao` dependency, use `runTest` for coroutine-based tests, and check if `getUsers()` returns the expected data.

#### 3. **Testing ViewModels**

You can test your ViewModel logic (such as fetching data, calling repository methods, and updating UI state).

```kotlin
class UserViewModelTest {

    private lateinit var userViewModel: UserViewModel
    private lateinit var userRepository: UserRepository

    @Before
    fun setup() {
        userRepository = mock(UserRepository::class.java)
        userViewModel = UserViewModel(userRepository)
    }

    @Test
    fun testGetUsers() = runTest {
        val fakeUsers = listOf(User(1, "John Doe", "john@example.com"))
        whenever(userRepository.getUsers()).thenReturn(fakeUsers)

        userViewModel.getUsers()

        assertEquals(fakeUsers, userViewModel.users.value)
    }
}
```

In this test:
- We mock the repository method `getUsers()` to return a predefined list.
- Then, we call the `getUsers()` function in the ViewModel and check if the `users` state is updated correctly.

---

### 10.2 UI Testing in Jetpack Compose

UI testing is used to test the actual UI of your app to ensure it behaves correctly in different scenarios. Jetpack Compose uses **Compose Test Framework** for this purpose.

#### 1. **Add Compose Testing Dependencies**

Add the following dependencies for UI testing:

```groovy
dependencies {
    androidTestImplementation "androidx.compose.ui:ui-test-junit4:1.5.0"
    androidTestImplementation "androidx.compose.ui:ui-test-manifest:1.5.0"
}
```

#### 2. **Writing UI Tests**

Compose UI tests allow you to simulate user interactions, check UI elements, and assert UI states.

Example of a simple UI test:

```kotlin
@RunWith(AndroidJUnit4::class)
class UserListScreenTest {

    @get:Rule
    val composeTestRule = createComposeRule()

    @Test
    fun testUserList() {
        // Set the Composable content
        composeTestRule.setContent {
            UserListScreen(viewModel = mockViewModel())
        }

        // Simulate user interaction
        composeTestRule.onNodeWithText("Add User").performClick()

        // Assert that the UI updates accordingly
        composeTestRule.onNodeWithText("John Doe").assertIsDisplayed()
    }

    private fun mockViewModel(): UserViewModel {
        val userViewModel = mock(UserViewModel::class.java)
        val fakeUsers = listOf(User(1, "John Doe", "john@example.com"))
        whenever(userViewModel.users).thenReturn(fakeUsers)
        return userViewModel
    }
}
```

In this test:
- We use the `createComposeRule` to set up the UI testing environment.
- `setContent` is used to set the Composable that we want to test.
- `onNodeWithText` helps us find UI elements based on their text.
- `performClick` simulates a click action on the button.
- `assertIsDisplayed` ensures that the correct UI element is shown after the action.

#### 3. **Testing ViewModel with LiveData/StateFlow**

To test the dynamic state of your ViewModel, use `LiveData` or `StateFlow` in your test. Jetpack Compose has support for observing changes in `StateFlow`/`LiveData`.

Example:

```kotlin
@Test
fun testStateUpdate() = runTest {
    val userViewModel = mock(UserViewModel::class.java)
    val fakeUsers = listOf(User(1, "John Doe", "john@example.com"))
    whenever(userViewModel.users).thenReturn(fakeUsers)

    composeTestRule.setContent {
        UserListScreen(viewModel = userViewModel)
    }

    // Assert that the users are displayed in the UI
    composeTestRule.onNodeWithText("John Doe").assertIsDisplayed()
}
```

---

### 10.3 Instrumentation Testing (Integration Testing)

Instrumentation tests are used to test the app's UI on a real device or emulator, ensuring that it works in a real-world environment.

1. **Add Instrumentation Test Dependencies**

```groovy
dependencies {
    androidTestImplementation "androidx.test.espresso:espresso-core:3.5.0"
    androidTestImplementation "androidx.test.ext:junit:1.1.5"
}
```

2. **Example Instrumentation Test**

```kotlin
@RunWith(AndroidJUnit4::class)
class UserListActivityTest {

    @Test
    fun testUserListScreen() {
        // Launch the activity
        val scenario = launchActivity<UserListActivity>()

        // Simulate a click on the "Add User" button
        onView(withText("Add User")).perform(click())

        // Assert that the user is displayed
        onView(withText("John Doe")).check(matches(isDisplayed()))
    }
}
```

---

### 🧠 Interview Q&A Practice – Testing

1. **What is the purpose of unit testing in Android development?**
2. **How do you mock dependencies in a ViewModel for unit tests?**
3. **What are the differences between unit tests and UI tests in Jetpack Compose?**
4. **How do you test UI interactions in Jetpack Compose (e.g., button clicks, text input)?**
5. **What is the role of `ComposeTestRule` in UI testing?**
6. **How do you test dynamic data (e.g., LiveData or StateFlow) in a ViewModel?**
7. **Explain the purpose of `@RunWith(AndroidJUnit4::class)` in instrumentation tests.**

---

### 🔧 Mini Task to Try

**Goal**: Write a unit test for the `UserRepository` class that mocks the `UserDao` and verifies if the data returned is correct. Then, write a UI test to verify that users are correctly displayed in the `UserListScreen` composable.

---

### 🎯 Recap

- Set up **unit tests** for ViewModel and repository.
- Use **Mockito** to mock dependencies and simulate behavior in unit tests.
- Write **UI tests** using the **Compose Test Framework** to verify the UI state and interactions.
- Use **instrumentation tests** to test the app on a device or emulator.
