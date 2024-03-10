# Kotlin

+ [Input & output](#input--output)
+ [Functions](#functions)
+ [Variables](#variables)


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