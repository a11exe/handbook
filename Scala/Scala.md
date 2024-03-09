# Scala

* [Worksheet](#worksheet)
* [Basic methods](#basic-methods)
* [infix operator notation](#infix-operator-notation)
* [Functions](#functions)
* [High order functions](#high-order-functions)
* [Custom types](#custom-types)

## Worksheet
A worksheet is a Scala file that is evaluated on save, and the result of each expression is shown in a column to the right of your program.

## Basic methods
```scala
3.*(2)
(1: Int).toDouble
1.2.toInt
```
Scala has two types of variables:
+ `val` (value) creates an immutable variable (like final in Java)
+ `var` (variable) creates a mutable variable

blocks
```scala
val v3 = {
  val v1 = 1
  val v2 = v1 + 1
  v2 + 1
}
```

In Scala, the `Unit` type is a subtype of the AnyVal type. It denotes a method that doesn't return any meaningful result
```scala
var v4 = {}
v4 == ()
```

```scala
def ??? : Nothing = throw new NotImplementedError
```
If we don't want implement method we can use `???`
```scala
def f(a: String): String = ???  
```
<img src="./scala_types.png" alt="scala types" width="600"/>

## Infix operator notation
If a method takes a single argument, you can call it without the period after the instance and without parentheses around the argument.

For example, if I have a `Matrix` class and a `+` method to add matrices, element by element, 
I can write `matrix1.+(matrix2)` or use the less cluttered and more intuitive syntax, `matrix1 + matrix2`.

However, there was no restriction on what methods applied in Scala 2, so if I also had a plus method, then `matrix1 plus matrix2` was allowed.

## Functions
Functions are expressions that have parameters, and take arguments.
You can define an anonymous function.

On the left of `=>` is a list of parameters. On the right is an expression involving the parameters.
```scala
var sqr: Int => Int = (x: Int) => x * x

var sqr2: Int => Int = x => x * x
var sum: (Int, Int) => Int = _ + _

var sqr3 = new Function[Int, Int] {
  override def apply(v1: Int): Int = v1 * v1
}

var factorial: Int => Int =
  n => if (n == 0) 1 else n * factorial(n - 1)
```

## Custom types
