# Kotlin collections

+ [Transformations](#transformations)
+ [Filter](#filter)
+ [Plus and minus operators](#plus-and-minus-operators)
+ [Grouping](#grouping)
+ [Retrieve collection parts](#retrieve-collection-parts)
+ [Take and drop](#take-and-drop)
+ [Check element existence](#check-element-existence)
+ [Ordering](#ordering)
+ [Aggregate operations](#aggregate-operations)
+ [Write operations](#write-operations)
+ [List-specific operations](#list-specific-operations)
+ [Set-specific operations](#set-specific-operations)
+ [Map-specific operations](#map-specific-operations)

## Transformations

These functions build new collections from existing ones based on the transformation rules provided.

### Map
```kotlin
val numbers = setOf(1, 2, 3)
println(numbers.map { it * 3 })
println(numbers.mapIndexed { idx, value -> value * idx })

println(numbers.mapNotNull { if ( it == 2) null else it * 3 })
println(numbers.mapIndexedNotNull { idx, value -> if (idx == 0) null else value * idx } )

val numbersMap = mapOf("key1" to 1, "key2" to 2, "key3" to 3, "key11" to 11)
println(numbersMap.mapKeys { it.key.uppercase() })
println(numbersMap.mapValues { it.value + it.key.length })
```

### Zip

Zipping transformation is building pairs from elements with the same positions in both collections. 
In the Kotlin standard library, this is done by the `zip()` extension function.

```kotlin
val colors = listOf("red", "brown", "grey")
val animals = listOf("fox", "bear", "wolf")
println(colors zip animals) // [(red, fox), (brown, bear), (grey, wolf)]
```

### Associate
Association transformations allow building maps from the collection elements and certain values associated with them.

```kotlin
val numbers = listOf("one", "two", "three", "four")

println(numbers.associateBy { it.first().uppercaseChar() }) // {O=one, T=three, F=four}
println(numbers.associateBy(keySelector = { it.first().uppercaseChar() }, valueTransform = { it.length })) // {O=3, T=5, F=4}
```
### Flatten

Provide flat access to nested collection elements useful.

```kotlin
val numberSets = listOf(setOf(1, 2, 3), setOf(4, 5, 6), setOf(1, 2))
println(numberSets.flatten()) // [1, 2, 3, 4, 5, 6, 1, 2]

val containers = listOf(
    StringContainer(listOf("one", "two", "three")),
    StringContainer(listOf("four", "five", "six")),
    StringContainer(listOf("seven", "eight"))
)
println(containers.flatMap { it.values }) // [one, two, three, four, five, six, seven, eight]
```

### String representation

```kotlin
val numbers = listOf("one", "two", "three", "four")

println(numbers)    // [one, two, three, four]     
println(numbers.joinToString()) // one, two, three, four

val listString = StringBuffer("The list of numbers: ")
numbers.joinTo(listString)
println(listString) // The list of numbers: one, two, three, four

val numbers = listOf("one", "two", "three", "four")
println(numbers.joinToString(separator = " | ", prefix = "start: ", postfix = ": end")) // start: one | two | three | four: end
```

## Filter

### Filters

```kotlin
val numbersMap = mapOf("key1" to 1, "key2" to 2, "key3" to 3, "key11" to 11)
val filteredMap = numbersMap.filter { (key, value) -> key.endsWith("1") && value > 10}
println(filteredMap) // {key11=11}

val numbers = listOf("one", "two", "three", "four")

val filteredIdx = numbers.filterIndexed { index, s -> (index != 0) && (s.length < 5)  }
val filteredNot = numbers.filterNot { it.length <= 3 }

println(filteredIdx) // [two, four]
println(filteredNot) // [three, four]

val numbers = listOf(null, "one", "two", null)
numbers.filterNotNull().forEach {
    println(it.length)   // length is unavailable for nullable Strings
}
```

### Partition
Another filtering function – partition() – filters a collection by a predicate and keeps the elements that don't match it 
in a separate list. So, you have a Pair of Lists as a return value: the first list containing elements that match 
the predicate and the second one containing everything else from the original collection.

```kotlin
val numbers = listOf("one", "two", "three", "four")
val (match, rest) = numbers.partition { it.length > 3 }

println(match) // [three, four]
println(rest) // [one, two]
```

### Any
```kotlin
val numbers = listOf("one", "two", "three", "four")

println(numbers.any { it.endsWith("e") }) // true
println(numbers.none { it.endsWith("a") }) // true
println(numbers.all { it.endsWith("e") }) // false
```

## Plus and minus operators

+ The result of `plus` contains the elements from the original collection and from the second operand.
+ The result of `minus` contains the elements of the original collection except the elements from the second operand. 
If it's an element, minus removes its first occurrence; if it's a collection, all occurrences of its elements are removed.

```kotlin
val numbers = listOf("one", "two", "three", "four", "four")

val plusList = numbers + "five"
val minusList = numbers - listOf("three", "four")
println(plusList) // [one, two, three, four, four, five]
println(minusList) // [one, two]
```

## Grouping
The basic function `groupBy()` takes a lambda function and returns a `Map`

```kotlin
val numbers = listOf("one", "two", "three", "four", "five")

println(numbers.groupBy { it.first().uppercase() }) // {O=[one], T=[two, three], F=[four, five]}
println(numbers.groupBy(keySelector = { it.first() }, valueTransform = { it.uppercase() })) // {o=[ONE], t=[TWO, THREE], f=[FOUR, FIVE]}
```

## Retrieve collection parts

### Slice
`slice()` returns a list of the collection elements with given indices. 
The indices may be passed either as a range or as a collection of integer values.

```kotlin
val numbers = listOf("one", "two", "three", "four", "five", "six")    
println(numbers.slice(1..3)) // [two, three, four]
println(numbers.slice(0..4 step 2)) // [one, three, five]
println(numbers.slice(setOf(3, 5, 0)))    // [four, six, one]
```

### Take and drop

To get the specified number of elements starting from the first, use the `take()` function. 
For getting the last elements, use `takeLast()`. 
When called with a number larger than the collection size, both functions return the whole collection.

To take all the elements except a given number of first or last elements, call the `drop()` and `dropLast()` functions respectively.

```kotlin
val numbers = listOf("one", "two", "three", "four", "five", "six")
println(numbers.take(3)) // [one, two, three]
println(numbers.takeLast(3)) // [four, five, six]
println(numbers.drop(1)) // [two, three, four, five, six]
println(numbers.dropLast(5)) // [one]
```

can be used with predicates
```kotlin
val numbers = listOf("one", "two", "three", "four", "five", "six")
println(numbers.takeWhile { !it.startsWith('f') }) // [one, two, three]
println(numbers.takeLastWhile { it != "three" }) // [four, five, six]
println(numbers.dropWhile { it.length == 3 }) // [three, four, five, six]
println(numbers.dropLastWhile { it.contains('i') }) // [one, two, three, four]
```

### Chunked

To break a collection into parts of a given size, use the `chunked()`

```kotlin
val numbers = (0..13).toList()
println(numbers.chunked(3)) // [[0, 1, 2], [3, 4, 5], [6, 7, 8], [9, 10, 11], [12, 13]]

val numbers = (0..13).toList()
println(numbers.chunked(3) { it.sum() })  // `it` is a chunk of the original collection
// [3, 12, 21, 30, 25]
```

### Windowed
You can retrieve all possible ranges of the collection elements of a given size.

```kotlin
val numbers = listOf("one", "two", "three", "four", "five")    
println(numbers.windowed(3)) // [one, two, three], [two, three, four], [three, four, five]]
```

### Retrive by position
```kotlin
val numbers = linkedSetOf("one", "two", "three", "four", "five")
println(numbers.elementAt(3))   // four
println(numbers.first()) // one
println(numbers.last()) // five
println(numbers.elementAtOrNull(5)) // null
println(numbers.elementAtOrElse(5) { index -> "The value for index $index is undefined"})
```

### Retrive by position
```kotlin
val numbers = listOf("one", "two", "three", "four", "five", "six")
println(numbers.first { it.length > 3 }) // three
println(numbers.last { it.startsWith("f") }) // five
println(numbers.random())
```

## Check element existence

```kotlin
val numbers = listOf("one", "two", "three", "four", "five", "six")
println(numbers.contains("four")) // true
println("zero" in numbers) // false

println(numbers.containsAll(listOf("four", "two"))) // true
println(numbers.containsAll(listOf("one", "zero"))) // false
println(numbers.isEmpty())
println(numbers.isNotEmpty())
```

## Ordering

### Sorting
```kotlin
val lengthComparator = Comparator { str1: String, str2: String -> str1.length - str2.length }
println(listOf("aaa", "bb", "c").sortedWith(lengthComparator)) // [c, bb, aaa]
println(listOf("aaa", "bb", "c").sortedWith(compareBy { it.length })) // [c, bb, aaa]

val numbers = listOf("one", "two", "three", "four")

println("Sorted ascending: ${numbers.sorted()}") // Sorted ascending: [four, one, three, two]
println("Sorted descending: ${numbers.sortedDescending()}") // Sorted descending: [two, three, one, four]

val sortedNumbers = numbers.sortedBy { it.length }
println("Sorted by length ascending: $sortedNumbers")
```

### Reverse order

```kotlin
val numbers = listOf("one", "two", "three", "four")
println(numbers.reversed()) // [four, three, two, one]
```

### Random order

```kotlin
val numbers = listOf("one", "two", "three", "four")
println(numbers.shuffled())
```

## Aggregate operations

### Simple functions
+ `minOrNull()` and `maxOrNull()` return the smallest and the largest element respectively. On empty collections, they return null.
+ `average()` returns the average value of elements in the collection of numbers.
+ `sum()` returns the sum of elements in the collection of numbers.
+ `count()` returns the number of elements in a collection.

### Fold and reduce

For more specific cases, there are the functions `reduce()` and `fold()` that apply the provided operation to the collection 
elements sequentially and return the accumulated result. 
The operation takes two arguments: the previously accumulated value and the collection element.

The difference between the two functions is that `fold()` takes an initial value and uses it as the accumulated value on 
the first step, whereas the first step of `reduce()` uses the first and the second elements as operation arguments 
on the first step.

```kotlin
val numbers = listOf(5, 2, 10, 4)

val simpleSum = numbers.reduce { sum, element -> sum + element }
println(simpleSum) // 21
val sumDoubled = numbers.fold(0) { sum, element -> sum + element * 2 }
println(sumDoubled) // 42

val numbers = listOf(5, 2, 10, 4)
val sumDoubledRight = numbers.foldRight(0) { element, sum -> sum + element * 2 }
println(sumDoubledRight) // 42
```

## Write operations
Mutable collections support operations for changing the collection contents, for example, adding or removing elements. 

### Add
```kotlin
val numbers = mutableListOf(1, 2, 3, 4)
numbers.add(5)
println(numbers) // [1, 2, 3, 4, 5]

val numbers = mutableListOf(1, 2, 5, 6)
numbers.addAll(arrayOf(7, 8))
println(numbers) // [1, 2, 5, 6, 7, 8]
numbers.addAll(2, setOf(3, 4)) 
println(numbers) // [1, 2, 3, 4, 5, 6, 7, 8]

val numbers = mutableListOf("one", "two")
numbers += "three"
println(numbers) // [one, two, three]
numbers += listOf("four", "five")
println(numbers) // [one, two, three, four, five]
```
### Remove
```kotlin
val numbers = mutableListOf(1, 2, 3, 4, 3)
numbers.remove(3)                    // removes the first `3`
println(numbers)
numbers.remove(5)                    // removes nothing
println(numbers)

val numbers = mutableListOf(1, 2, 3, 4)
println(numbers) // [1, 2, 3, 4]
numbers.retainAll { it >= 3 }
println(numbers) // [3, 4]
numbers.clear()
println(numbers) // []

val numbersSet = mutableSetOf("one", "two", "three", "four")
numbersSet.removeAll(setOf("one", "two"))
println(numbersSet) // [three, four]

val numbers = mutableListOf("one", "two", "three", "three", "four")
numbers -= "three"
println(numbers) // [one, two, three, four]
```

## List-specific operations

### Retrieve element

```kotlin
val numbers = listOf(1, 2, 3, 4)
println(numbers.get(0))
println(numbers[0])
//numbers.get(5)                         // exception!
println(numbers.getOrNull(5))             // null
println(numbers.getOrElse(5, {it}))        // 5
```

extract part
```kotlin
val numbers = (0..13).toList()
println(numbers.subList(3, 6)) // [3, 4, 5]
```

### Linear search
```kotlin
val numbers = listOf(1, 2, 3, 4, 2, 5)
println(numbers.indexOf(2)) // 1
println(numbers.lastIndexOf(2)) // 4

val numbers = mutableListOf(1, 2, 3, 4)
println(numbers.indexOfFirst { it > 2}) // 2
println(numbers.indexOfLast { it % 2 == 1}) // 2
```
### Binary search in sorted lists

```kotlin
val numbers = mutableListOf("one", "two", "three", "four")
numbers.sort()
println(numbers)
println(numbers.binarySearch("two"))  // 3
println(numbers.binarySearch("z")) // -5
println(numbers.binarySearch("two", 0, 2))  // -3 search between provided indices
```

### write operations

```kotlin
val numbers = mutableListOf("one", "five", "six")
numbers.add(1, "two")
numbers.addAll(2, listOf("three", "four"))
println(numbers) // [one, two, three, four, five, six]

val numbers = mutableListOf("one", "five", "three")
numbers[1] =  "two"
println(numbers) // [one, two, three]

val numbers = mutableListOf(1, 2, 3, 4)
numbers.fill(3)
println(numbers) // [3, 3, 3, 3]

val numbers = mutableListOf(1, 2, 3, 4, 3)
numbers.removeAt(1)
println(numbers) // [1, 3, 4, 3]
```

## Set-specific operations

```kotlin
val numbers = setOf("one", "two", "three")

// output according to the order
println(numbers union setOf("four", "five"))
// [one, two, three, four, five]
println(setOf("four", "five") union numbers)
// [four, five, one, two, three]

val numbers = setOf("one", "two", "three")

// same output
println(numbers intersect setOf("two", "one"))
// [one, two]
println(numbers subtract setOf("three", "four"))
// [one, two]
println(numbers subtract setOf("four", "three"))
// [one, two]

val numbers = setOf("one", "two", "three")
val numbers2 = setOf("three", "four")

// merge differences 
println((numbers - numbers2) union (numbers2 - numbers))
// [one, two, four]

val list1 = listOf(1, 1, 2, 3, 5, 8, -1)
val list2 = listOf(1, 1, 2, 2, 3, 5)

// result of intersecting two lists is a Set
println(list1 intersect list2)
//[1, 2, 3, 5]

// equal elements are merged into one
println(list1 union list2)
// [1, 2, 3, 5, 8, -1]
```

## Map-specific operations

### Retrieve value
```kotlin
val numbersMap = mapOf("one" to 1, "two" to 2, "three" to 3)
println(numbersMap.get("one")) // 1
println(numbersMap["one"]) // 1
println(numbersMap.getOrDefault("four", 10)) // 10
println(numbersMap["five"])               // null
//numbersMap.getValue("six")      // exception!

val numbersMap = mapOf("one" to 1, "two" to 2, "three" to 3)
println(numbersMap.keys) // [one, two, three]
println(numbersMap.values) // [1, 2, 3]
```

### Filter
```kotlin
val numbersMap = mapOf("key1" to 1, "key2" to 2, "key3" to 3, "key11" to 11)
val filteredMap = numbersMap.filter { (key, value) -> key.endsWith("1") && value > 10}
println(filteredMap) // {key11=11}

val numbersMap = mapOf("key1" to 1, "key2" to 2, "key3" to 3, "key11" to 11)
val filteredKeysMap = numbersMap.filterKeys { it.endsWith("1") }
val filteredValuesMap = numbersMap.filterValues { it < 10 }
```

### Plus and minus operators
```kotlin
val numbersMap = mapOf("one" to 1, "two" to 2, "three" to 3)
println(numbersMap + Pair("four", 4)) // {one=1, two=2, three=3, four=4}
println(numbersMap + Pair("one", 10)) // {one=10, two=2, three=3}
println(numbersMap + mapOf("five" to 5, "one" to 11)) // {one=11, two=2, three=3, five=5}

val numbersMap = mapOf("one" to 1, "two" to 2, "three" to 3)
println(numbersMap - "one") // {two=2, three=3}
println(numbersMap - listOf("two", "four")) // {one=1, three=3}
```

### Write
```kotlin
val numbersMap = mutableMapOf("one" to 1, "two" to 2)
numbersMap.put("three", 3)
println(numbersMap) // {one=1, two=2, three=3}

val numbersMap = mutableMapOf("one" to 1, "two" to 2, "three" to 3)
numbersMap.putAll(setOf("four" to 4, "five" to 5))
println(numbersMap) // {one=1, two=2, three=3, four=4, five=5}
```

### Remove
```kotlin
val numbersMap = mutableMapOf("one" to 1, "two" to 2, "three" to 3)
numbersMap.remove("one")
println(numbersMap) // {two=2, three=3}
numbersMap.remove("three", 4)            //doesn't remove anything
println(numbersMap) // {two=2, three=3}

val numbersMap = mutableMapOf("one" to 1, "two" to 2, "three" to 3, "threeAgain" to 3)
numbersMap.keys.remove("one")
println(numbersMap) // {two=2, three=3, threeAgain=3}
numbersMap.values.remove(3)
println(numbersMap) // {two=2, threeAgain=3}
```