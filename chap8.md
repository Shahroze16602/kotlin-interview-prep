### 8. Room & Data Persistence

Room is a persistence library that provides an abstraction layer over SQLite. It helps you work with databases in a more efficient and less error-prone way. It integrates well with Jetpack Compose and is a great choice for storing local data.

### 8.1 Setup Room in Your Project

1. **Add Dependencies**:

In your `build.gradle` (app-level) file, add the following Room dependencies:

```groovy
dependencies {
    implementation "androidx.room:room-runtime:2.5.1"
    implementation "androidx.room:room-ktx:2.5.1"
    kapt "androidx.room:room-compiler:2.5.1"
}
```

2. **Enable KAPT (Kotlin Annotation Processing Tool)** in the same `build.gradle` file:

```groovy
apply plugin: 'kotlin-kapt'
```

### 8.2 Define Room Entities and DAO

1. **Define a Data Class as an Entity**:

Room uses annotations to define entities and the corresponding table schema in SQLite.

```kotlin
@Entity(tableName = "users")
data class User(
    @PrimaryKey val id: Int,
    @ColumnInfo(name = "name") val name: String,
    @ColumnInfo(name = "email") val email: String
)
```

2. **Create a DAO (Data Access Object)**:

DAO provides methods to interact with the database. You define queries here.

```kotlin
@Dao
interface UserDao {

    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insertUser(user: User)

    @Query("SELECT * FROM users")
    suspend fun getUsers(): List<User>
}
```

3. **Define the Room Database**:

```kotlin
@Database(entities = [User::class], version = 1, exportSchema = false)
abstract class AppDatabase : RoomDatabase() {
    abstract fun userDao(): UserDao
}
```

### 8.3 Setting Up Room Database

1. **Initialize Room Database**:

In your `Application` class or `Activity`, initialize Room and create the database instance:

```kotlin
@HiltAndroidApp
class MyApp : Application() {
    val database: AppDatabase by lazy {
        Room.databaseBuilder(applicationContext, AppDatabase::class.java, "app_database")
            .build()
    }
}
```

You can also use Hilt to inject the database instance.

### 8.4 Create a Repository Layer

1. **Create a Repository to Manage Data Operations**:

```kotlin
class UserRepository @Inject constructor(private val userDao: UserDao) {

    suspend fun getUsers(): List<User> = userDao.getUsers()

    suspend fun addUser(user: User) = userDao.insertUser(user)
}
```

### 8.5 Using Room with ViewModel

1. **ViewModel Setup**:

In your `ViewModel`, use the `UserRepository` to interact with the database:

```kotlin
@HiltViewModel
class UserViewModel @Inject constructor(private val userRepository: UserRepository) : ViewModel() {

    private val _users = mutableStateOf<List<User>>(emptyList())
    val users: State<List<User>> = _users

    init {
        viewModelScope.launch {
            _users.value = userRepository.getUsers()
        }
    }

    fun addUser(user: User) {
        viewModelScope.launch {
            userRepository.addUser(user)
            _users.value = userRepository.getUsers()
        }
    }
}
```

### 8.6 Display Data in Jetpack Compose

You can now observe data in your Composables and display it:

```kotlin
@Composable
fun UserListScreen(viewModel: UserViewModel = hiltViewModel()) {
    val users = viewModel.users.value

    LazyColumn {
        items(users) { user ->
            Text(text = "Name: ${user.name}, Email: ${user.email}")
        }
    }

    Button(onClick = { viewModel.addUser(User(3, "John Doe", "john@example.com")) }) {
        Text("Add User")
    }
}
```

In this example:
- The `UserListScreen` composable observes the `users` state in the `UserViewModel`.
- A button is provided to add a new user to the Room database, which updates the displayed list.

### 8.7 Handling Database Operations in Background Threads

Since database operations are I/O bound and may block the main thread, they should always be done in a background thread (which is handled by `viewModelScope.launch` in the example above). 

### 8.8 Room Migration

When you modify the schema of your database (e.g., adding new columns), you need to handle migrations. Room supports schema migrations to ensure backward compatibility.

Example of adding a new column:

```kotlin
val MIGRATION_1_2 = object : Migration(1, 2) {
    override fun migrate(database: SupportSQLiteDatabase) {
        database.execSQL("ALTER TABLE users ADD COLUMN age INTEGER NOT NULL DEFAULT 0")
    }
}
```

And then add it to the Room database builder:

```kotlin
Room.databaseBuilder(context, AppDatabase::class.java, "app_database")
    .addMigrations(MIGRATION_1_2)
    .build()
```

### 🧠 Interview Q&A Practice – Room & Data Persistence

1. **What is the difference between Room and SQLite? Why should I use Room in an Android app?**
2. **How does Room handle database migrations?**
3. **What are the benefits of using a DAO in Room?**
4. **How would you implement a one-to-many relationship in Room?**
5. **What is the role of `@Insert`, `@Update`, `@Delete`, and `@Query` annotations in Room?**
6. **How would you manage database operations in a `ViewModel` while ensuring it doesn't block the UI thread?**
7. **Explain the purpose of `OnConflictStrategy` in Room. How would you handle conflicts when inserting data?**

---

### 🔧 Mini Task to Try

**Goal**: Create a simple app where users can add and view books in a Room database. Include fields like `id`, `title`, and `author`. The main screen should display a list of books, and a button should allow adding new books to the database.

---

### 🎯 Recap

- Set up Room with entities, DAO, and database.
- Created a `Repository` layer to interact with the database.
- Used `ViewModel` to manage data and provide it to Jetpack Compose.
- Implemented adding and viewing data in a simple UI.
