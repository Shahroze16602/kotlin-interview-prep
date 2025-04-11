## 2. Object-Oriented Programming (OOP) in Kotlin

Object-Oriented Programming (OOP) is a paradigm centered around objects that encapsulate data and behavior. Kotlin, being a modern language, fully supports OOP principles, allowing for clean and maintainable code.

### 2.1 Classes and Objects

In Kotlin, a class is a blueprint for creating objects. Here's how you define a simple class and create its instance:

```kotlin
class Car(val brand: String, var speed: Int) {
    fun accelerate() {
        speed += 10
    }
}

val myCar = Car("Toyota", 100)
myCar.accelerate()
println("${myCar.brand} is going at ${myCar.speed} km/h")
```

### 2.2 Inheritance

Kotlin supports single inheritance, allowing a class to inherit from another:

```kotlin
open class Animal(val name: String) {
    open fun sound() = println("$name makes a sound")
}

class Dog(name: String) : Animal(name) {
    override fun sound() = println("$name barks")
}

val dog = Dog("Buddy")
dog.sound()
```

### 2.3 Interfaces

Interfaces define a contract that classes can implement:

```kotlin
interface Drivable {
    fun drive()
}

class Bicycle : Drivable {
    override fun drive() = println("Bicycle is moving")
}

val bike = Bicycle()
bike.drive()
```

### 2.4 Encapsulation

Encapsulation is achieved by restricting access to class members:

```kotlin
class BankAccount(private var balance: Double) {
    fun deposit(amount: Double) {
        if (amount > 0) balance += amount
    }

    fun getBalance() = balance
}

val account = BankAccount(1000.0)
account.deposit(500.0)
println("Balance: ${account.getBalance()}")
```

### 2.5 Polymorphism

Polymorphism allows objects to be treated as instances of their parent class:

```kotlin
open class Shape {
    open fun draw() = println("Drawing a shape")
}

class Circle : Shape() {
    override fun draw() = println("Drawing a circle")
}

fun render(shape: Shape) {
    shape.draw()
}

val circle = Circle()
render(circle) // Outputs: Drawing a circle
```

### 2.6 Abstraction

Abstraction is implemented using abstract classes and methods:

```kotlin
abstract class Vehicle {
    abstract fun start()
}

class Car : Vehicle() {
    override fun start() = println("Car is starting")
}

val car = Car()
car.start()
```

### 2.7 Delegation

Kotlin provides built-in support for delegation using the `by` keyword:

```kotlin
interface Engine {
    fun start()
}

class DieselEngine : Engine {
    override fun start() = println("Diesel engine starting")
}

class Truck(engine: Engine) : Engine by engine

val engine = DieselEngine()
val truck = Truck(engine)
truck.start() // Outputs: Diesel engine starting
```

---

## 3. Functional Programming (FP) in Kotlin

Functional Programming emphasizes immutability and pure functions. Kotlin blends OOP and FP seamlessly.

### 3.1 Pure Functions

Pure functions have no side effects and return the same output for the same input:

```kotlin
fun add(a: Int, b: Int) = a + b
```

### 3.2 Immutability

Prefer using immutable data structures:

```kotlin
val numbers = listOf(1, 2, 3) // Immutable list
```

### 3.3 First-Class and Higher-Order Functions

Functions can be assigned to variables and passed as parameters:

```kotlin
val greet: (String) -> String = { name -> "Hello, $name" }
println(greet("Shahroze"))

fun operate(a: Int, b: Int, operation: (Int, Int) -> Int): Int {
    return operation(a, b)
}

val sum = operate(3, 4) { x, y -> x + y }
println(sum) // Outputs: 7
```

### 3.4 Lambda Expressions

Lambdas provide a concise way to represent functions:

```kotlin
val square = { x: Int -> x * x }
println(square(5)) // Outputs: 25
```

### 3.5 Collection Operations

Kotlin's standard library offers functional operations on collections:

```kotlin
val numbers = listOf(1, 2, 3, 4, 5)
val doubled = numbers.map { it * 2 }
val even = numbers.filter { it % 2 == 0 }
val sum = numbers.reduce { acc, i -> acc + i }
```

### 3.6 Function Composition

Combine functions to build complex operations:

```kotlin
fun <A, B, C> compose(f: (B) -> C, g: (A) -> B): (A) -> C = { x -> f(g(x)) }

val double = { x: Int -> x * 2 }
val square = { x: Int -> x * x }
val doubleThenSquare = compose(square, double)

println(doubleThenSquare(3)) // Outputs: 36
```

---

## Practice Tasks

1. **OOP Task**: Create a class hierarchy with a base class `Employee` and subclasses `Manager` and `Developer`. Implement methods to calculate salary based on role.

2. **FP Task**: Given a list of integers, use functional operations to filter out even numbers, square the remaining, and calculate their sum.

3. **Delegation Task**: Implement a class `Printer` that delegates its printing functionality to another class `ConsolePrinter`.
