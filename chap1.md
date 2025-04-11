Absolutely! Let's begin with a comprehensive and detailed exploration of **Kotlin Fundamentals**, complete with examples and practical tasks to solidify your understanding.

---

## 1. Kotlin Fundamentals

Kotlin is a modern, statically typed programming language that enhances productivity and developer satisfaction. Key features include:

- **Conciseness**: Reduces boilerplate code, making it more readable and maintainable.
- **Null Safety**: Eliminates null pointer exceptions by distinguishing nullable and non-nullable types.
- **Interoperability**: Fully interoperable with Java, allowing seamless integration with existing codebases.
- **Extension Functions**: Enables adding new functions to existing classes without modifying their source code.
- **Coroutines**: Simplifies asynchronous programming by providing a more straightforward and efficient way to handle concurrency.

For a comprehensive understanding, consider exploring the [Android Kotlin Fundamentals course](https://developer.android.com/courses/pathways/android-basics-compose-unit-2-pathway-1).

---

### 1.1 Variables and Data Types

In Kotlin, variables are declared using `val` for immutable references and `var` for mutable ones.

```kotlin
val name: String = "Shahroze" // Immutable
var age: Int = 25             // Mutable
```

Kotlin supports type inference, allowing you to omit the type declaration:

```kotlin
val city = "Multan" // Inferred as String
```

---

### 1.2 Control Flow

#### If-Else Expression

```kotlin
val max = if (a > b) a else b
```

#### When Expression (Similar to switch-case)

```kotlin
val result = when (score) {
    in 90..100 -> "A"
    in 80..89  -> "B"
    else       -> "F"
}
```

---

### 1.3 Functions

Functions in Kotlin are declared using the `fun` keyword:

```kotlin
fun greet(name: String): String {
    return "Hello, $name!"
}
```

Kotlin also supports single-expression functions:

```kotlin
fun greet(name: String) = "Hello, $name!"
```

---

### 1.4 Null Safety

Kotlin distinguishes between nullable and non-nullable types:

```kotlin
var name: String = "Shahroze" // Non-nullable
var nickname: String? = null  // Nullable
```

To safely access nullable types, use the safe call operator `?.`:

```kotlin
val length = nickname?.length
```

The Elvis operator `?:` provides a default value:

```kotlin
val length = nickname?.length ?: 0
```

---

### 1.5 Collections

Kotlin provides various collection types:

```kotlin
val numbers = listOf(1, 2, 3)        // Immutable list
val mutableNumbers = mutableListOf(1, 2, 3) // Mutable list
```

You can iterate over collections using loops:

```kotlin
for (number in numbers) {
    println(number)
}
```

---

### 1.6 Lambdas and Higher-Order Functions

Kotlin supports functional programming constructs:

```kotlin
val numbers = listOf(1, 2, 3, 4, 5)
val doubled = numbers.map { it * 2 }
```

Here, `map` is a higher-order function that applies the lambda `{ it * 2 }` to each element.

---

### 1.7 Extension Functions

Extension functions allow you to add functionality to existing classes:

```kotlin
fun String.addExclamation(): String {
    return this + "!"
}

val excited = "Hello".addExclamation() // "Hello!"
```

---

### 1.8 Data Classes

Data classes are used to hold data:

```kotlin
data class User(val name: String, val age: Int)

val user = User("Shahroze", 25)
```

They automatically provide `equals()`, `hashCode()`, `toString()`, and `copy()` functions.

---

### 1.9 Sealed Classes

Sealed classes are used for representing restricted class hierarchies:

```kotlin
sealed class Result
data class Success(val data: String) : Result()
data class Error(val exception: Exception) : Result()
```

They are useful in `when` expressions:

```kotlin
fun handle(result: Result) {
    when (result) {
        is Success -> println(result.data)
        is Error   -> println(result.exception)
    }
}
```

---

### 1.10 Companion Objects

Companion objects allow you to define static members:

```kotlin
class MyClass {
    companion object {
        const val CONSTANT = "constant value"
    }
}

val value = MyClass.CONSTANT
```

---

### 1.11 Practice Tasks

1. **Palindrome Checker**: Write a function to check if a given string is a palindrome.

2. **Fibonacci Sequence**: Generate the first `n` numbers in the Fibonacci sequence.

3. **List Filtering**: Given a list of integers, filter out the even numbers and square the remaining ones.

4. **Null Handling**: Create a nullable string variable and safely print its length, providing a default value if it's null.

5. **User Data Class**: Define a `User` data class with `name` and `email` properties. Create a list of users and print their names.

---

Once you've completed these tasks, feel free to share your solutions, and we can discuss them together. Let me know when you're ready to proceed to the next topic: **Object-Oriented & Functional Concepts in Kotlin**. 
