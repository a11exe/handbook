# Kotlin

+ [Decompile](#decompile)
+ [Input & output](#input--output)
+ [Functions](#functions)
+ [Infix Notation](#infix-notation)
+ [Variables](#variables)
+ [Null safety](#null-safety)
+ [Classes and instances](#classes-and-instances)
+ [Conditional expressions](#conditional-expressions)
+ [Loops](#loops)
+ [Collections](#collections)
+ [DTO](#dto)
+ [Default values for function parameters](#default-values-for-function-parameters)
+ [Lazy property](#lazy-property)
+ [Extension functions](#extension-functions)
+ [Try-catch expression](#try-catch-expression)
+ [Single-expression functions](#single-expression-functions)
+ [Call multiple methods on an object instance (with)](#call-multiple-methods-on-an-object-instance-with)
+ [Configure properties of an object (apply)](#configure-properties-of-an-object-apply)
+ [Mark code as incomplete (TODO)](#mark-code-as-incomplete-todo)
+ [Callbacks](#callbacks)
+ [Coroutines](./Coroutines.md)
+ [Companion Object](#companion-object)
+ [Object](#object)

## Decompile

Tools → Kotlin → Show Kotlin Bytecode → Decompile

## Input & output

```kotlin
println("Hello from Kotlin. Enter your name:")

val name = readln()
print("Hello ${name}")
```

## Functions

```kotlin
fun sum(a: Int, b: Int): Int {
    return a + b
}

fun sum(a: Int, b: Int) = a + b
```

## Infix Notation
Kotlin allows some functions to be called without using the period and brackets. These are called infix methods, and their use can result in code that looks much more like a natural language.

This is most commonly seen in the inline Map definition:

```kotlin
map(
  1 to "one",
  2 to "two",
  3 to "three"
)
```

Writing an infix function is a simple case of following three rules:

+ The function is either defined on a class or is an extension method for a class
+ The function takes exactly one parameter
+ The function is defined using the infix keyword
```kotlin
class Assertion<T>(private val target: T) {
    infix fun isEqualTo(other: T) {
        Assert.assertEquals(other, target)
    }

    infix fun isDifferentFrom(other: T) {
        Assert.assertNotEquals(other, target)
    }
}
```

using
```kotlin
val result = Assertion(5)

result isEqualTo 5 // This passes
result isEqualTo 6 // This fails the assertion
result isDifferentFrom 5 // This also fails the assertion
```

**Note that infix functions can also be written as extension methods to existing classes.**
```kotlin
infix fun String.substringMatches(r: Regex) : List<String> {
    return r.findAll(this)
      .map { it.value }
      .toList()
}

val matches = "a bc def" substringMatches ".*? ".toRegex()
Assert.assertEquals(listOf("a ", "bc "), matches)
```

## Variables
Read-only local variables are defined using the keyword val. They can be assigned a value only once.

```kotlin
val a: Int = 1  // immediate assignment
val b = 2   // `Int` type is inferred
val c: Int  // Type required when no initializer is provided
val unsignedInt: UInt = 1234u // unsigned types
```

Variables that can be reassigned use the var keyword.

```kotlin
var x = 5 // `Int` type is inferred
x += 1
```

## Null safety

```kotlin
var a: String = "abc" // Regular initialization means non-nullable
val l = a.length // it's guaranteed not to cause an NPE
var b: String? = "abc" // can be set to null
println(b?.length) // safe call This returns b.length if b is not null, and null otherwise
bob?.department?.head?.name // Such a chain returns null if any of the properties in it is null
```

To perform a certain operation only for non-null values, you can use the safe call operator together with `let`
```kotlin
val listWithNulls: List<String?> = listOf("Kotlin", null)
for (item in listWithNulls) {
    item?.let { println(it) } // prints Kotlin and ignores null
}
// If either `person` or `person.department` is null, the function is not called:
person?.department?.head = managersPool.getManager()
```

default value
```kotlin
val age: Int = nullableAge ?: 30
```

Elvis operator
```kotlin
val l: Int = if (b != null) b.length else -1
val l = b?.length ?: -1
val name = node.getName() ?: throw IllegalArgumentException("name expected")
```

The !! operator. Converts any value to a non-nullable type and throws an exception if the value is null
```kotlin
val l = b!!.length
```

Safe casts
Regular casts may result in a ClassCastException if the object is not of the target type. 
Another option is to use safe casts that return null if the attempt was not successful:
```kotlin
val aInt: Int? = a as? Int
```

Collections of a nullable type
```kotlin
val nullableList: List<Int?> = listOf(1, 2, null, 4)
val intList: List<Int> = nullableList.filterNotNull()
```

## Classes and instances

Create class
```kotlin
class Rectangle(val height: Double, val length: Double) {
    val perimeter = (height + length) * 2
}
```

Create instance
```kotlin
val rectangle = Rectangle(5.0, 2.0)
println("The perimeter is ${rectangle.perimeter}")
```

Classes are final by default; to make a class inheritable, mark it as `open`.
```kotlin
open class Shape
```

Inheritance between classes is declared by a colon (`:`).
```kotlin
class Rectangle(val height: Double, val length: Double): Shape() {
    val perimeter = (height + length) * 2
}
```

## Conditional expressions

if
```kotlin
fun maxOf(a: Int, b: Int): Int {
    if (a > b) {
        return a
    } else {
        return b
    }
}
```

short if
```kotlin
fun maxOf(a: Int, b: Int) = if (a > b) a else b
```

if with result
```kotlin
val y = if (x == 1) {
    "one"
} else if (x == 2) {
    "two"
} else {
    "other"
}
```

## Loops

for loop
```kotlin
val items = listOf("apple", "banana", "kiwifruit")
for (item in items) {
    println(item)
}
for (index in items.indices) {
    println("item at $index is ${items[index]}")
}
```

while loop
```kotlin
val items = listOf("apple", "banana", "kiwifruit")
var index = 0
while (index < items.size) {
    println("item at $index is ${items[index]}")
    index++
}
```

when expression

```kotlin
fun describe(obj: Any): String =
    when (obj) {
        1          -> "One"
        "Hello"    -> "Greeting"
        is Long    -> "Long"
        !is String -> "Not a string"
        else       -> "Unknown"
    }
```

ranges
```kotlin
val x = 10
val y = 9
if (x in 1..y+1) {
    println("fits in range")
}
```

iterate over range
```kotlin
for (x in 1..5) {
    print(x)
}
```
Or over a progression.
```kotlin
for (x in 1..10 step 2) {
    print(x)
}
println()
for (x in 9 downTo 0 step 3) {
    print(x)
}
```

## Collections

[Kotlin collections](./Collections.md)

Iterate over a collection.
```kotlin
for (item in items) {
    println(item)
}

val numbers = listOf("one", "two", "three", "four")
numbers.forEach {
    println(it)
}
```

Check if a collection contains an object using in operator:
```kotlin
when {
    "orange" in items -> println("juicy")
    "apple" in items -> println("apple is fine too")
}
```

Use lambda expressions to filter and map collections:
```kotlin
val fruits = listOf("banana", "avocado", "apple", "kiwifruit")
fruits
    .filter { it.startsWith("a") }
    .sortedBy { it }
    .map { it.uppercase() }
    .forEach { println(it) }
```

If you provide a comma-separated list of collection elements as arguments, the compiler detects the element type automatically. 
When creating empty collections, specify the type explicitly.
```kotlin
val numbersSet = setOf("one", "two", "three", "four")
val emptySet = mutableSetOf<String>()
```

map
Note that the to notation creates a short-living Pair object, so it's recommended that you use it only if performance isn't critical. 
To avoid excessive memory usage, use alternative ways. For example, you can create a mutable map and populate it using the write operations. 
The `apply()` function can help to keep the initialization fluent here.
```kotlin
val numbersMap = mapOf("key1" to 1, "key2" to 2, "key3" to 3, "key4" to 1)
val numbersMap = mutableMapOf<String, String>().apply { this["one"] = "1"; this["two"] = "2" }
```

Another way of creating a collection is to call a builder function – `buildList()`, `buildSet()`, or `buildMap()`. 
They create a new, mutable collection of the corresponding type
```kotlin
val map = buildMap { // this is MutableMap<String, Int>, types of key and value are inferred from the `put()` calls below
    put("a", 1)
    put("b", 0)
    put("c", 4)
}

println(map) // {a=1, b=0, c=4}
```

Empty collections
```kotlin
val empty = emptyList<String>()
```

For lists, there is a constructor-like function that takes the list size and the initializer function that defines the element value based on its index.
```kotlin
val doubled = List(3, { it * 2 })  // or MutableList if you want to change its content later
println(doubled) // [0, 2, 4]
```

Concrete type constructors
```kotlin
val linkedList = LinkedList<String>(listOf("one", "two", "three"))
```

Collection copying functions, such as `toList()`, `toMutableList()`, `toSet()`
create a snapshot of a collection at a specific moment. Their result is a new collection of the same elements. 
If you add or remove elements from the original collection, this won't affect the copies.
```kotlin
val sourceList = mutableListOf(1, 2, 3)    
val copySet = sourceList.toMutableSet()
copySet.add(3)
copySet.add(4)    
println(copySet) // [1, 2, 3, 4]
```

Invoke functions on other collections
```kotlin
val numbers = listOf("one", "two", "three", "four")  
val longerThan3 = numbers.filter { it.length > 3 }

val numbers = setOf(1, 2, 3)
println(numbers.map { it * 3 })
println(numbers.mapIndexed { idx, value -> value * idx })

val numbers = listOf("one", "two", "three", "four")
println(numbers.associateWith { it.length }) // {one=3, two=3, three=5, four=4}
```

## DTO
Create DTOs (POJOs/POCOs)

```kotlin
data class Customer(val name: String, val email: String)

data class User(val name: String = "", val age: Int = 0)
```

To exclude a property from the generated implementations, declare it inside the class body
(`.equals()` only evaluates properties from the primary constructor):
```kotlin
data class Person(val name: String) {
    var age: Int = 0
}
```

provides a Customer class with the following functionality:
getters (and setters in case of vars) for all properties
+ `equals()`
+ `hashCode()`
+ `toString()`
+ `copy()`

## Default values for function parameters

```kotlin
fun foo(a: Int = 0, b: String = "") { ... }
```

## Lazy property
```kotlin
val p: String by lazy { // the value is computed only on first access
    // compute the string
}
```

## Extension functions
```kotlin
fun String.spaceToCamelCase() { ... }

"Convert this to camelcase".spaceToCamelCase()
```

## Try-catch expression
```kotlin
fun test() {
    val result = try {
        count()
    } catch (e: ArithmeticException) {
        throw IllegalStateException(e)
    }

    // Working with result
}
```

## Single-expression functions
```kotlin
fun theAnswer() = 42
```

the same as
```kotlin
fun theAnswer(): Int {
    return 42
}
```

## Call multiple methods on an object instance (with)
```kotlin
class Turtle {
    fun penDown()
    fun penUp()
    fun turn(degrees: Double)
    fun forward(pixels: Double)
}

val myTurtle = Turtle()
with(myTurtle) { //draw a 100 pix square
    penDown()
    for (i in 1..4) {
        forward(100.0)
        turn(90.0)
    }
    penUp()
}
```

## Configure properties of an object (apply)

This is useful for configuring properties that aren't present in the object constructor.

```kotlin
val myRectangle = Rectangle().apply {
    length = 4
    breadth = 5
    color = 0xFAFAFA
}
```

## Mark code as incomplete (TODO)

Kotlin's standard library has a TODO() function that will always throw a NotImplementedError. 
Its return type is Nothing so it can be used regardless of expected type. 
There's also an overload that accepts a reason parameter:

```kotlin
fun calcTaxes(): BigDecimal = TODO("Waiting for feedback from accounting")
```

## Callbacks
Callback functions in Kotlin play a core role in administering asynchronous techniques in our programs. 
With a callback function, we can execute code pieces asynchronously, as well as execute a certain piece of code when a particular event occurs.

Let’s consider an example where we want to pull some data from the server off the main thread. 
To achieve this, we can use a callback function that executes the remote loading of users from a server asynchronously:

```kotlin
data class User(var firstName: String, var lastName: String)
```

```kotlin
suspend fun loadUsersFromServer(callback: (List<User>) -> Unit) {
    delay(5000)
    val users = listOf(User("Flore", "P"), User("Nappy", "Sean"), User("Ndole", "Paul"))
    callback(users)
}
```

```kotlin
var listofUsers = emptyList<User>()
suspend fun executeLoading(){
    loadUsersFromServer { users ->
        listofUsers = users
    }
}
```
Now, a complete test of these functions will look like this:
```kotlin
@Test
fun `asynchronous callback to load remote data`(){
    runBlocking{
        executeLoading()
    }
    assertEquals(3, listofUsers.size)
    assertEquals("Flore", listofUsers[0].firstName)
    assertEquals("Sean", listofUsers[1].lastName)
    assertEquals("Ndole Paul", "${listofUsers[2].firstName} ${listofUsers[2].lastName}" )
}
```

## Companion Object

There are times we need to use a companion object to define class members that are going to be used independently of any instance of that class. 
The Kotlin compiler guarantees we will have one and only one instance of a companion object. 
For those of us with a background in Java and C#, a companion object is similar to static declarations.

defining
```kotlin
class ClassName {
    companion object {
        const val propertyName: String = "Something..."
        fun funName() {
            //...
        }
    }
}
```

using
```kotlin
val property = ClassName.propertyName
ClassName.funName()
```

So, if we need a Java-interoperable code, the solution is @JvmStatic functions and @JvmStatic properties. 
By annotating a companion object’s members with @JvmStatic, we will gain better Java interoperability:

```kotlin
companion object {
    @JvmStatic
    val propertyName: String = "Something..."
}
```

## Object

Object instead represents a single static instance, and can never have any more or any less than this one instance.

This is useful for various techniques, including singleton objects and simple packaging up of functionality for encapsulation:
```kotlin
object SimpleSingleton {
    val answer = 42;
    fun greet(name: String) = "Hello, $name!"
}

assertEquals(42, SimpleSingleton.answer)
assertEquals("Hello, world!", SimpleSingleton.greet("world"))
```
