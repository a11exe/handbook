# Java

Java SE 8, 11, 17 and 21 are LTS releases

* [Stack](#stack)
* [BitSet](#bitset)
* [Check Integer overflow](#check-integer-overflow)
* [JVM object sizes](#jvm-object-sizes)
* [Bitwise Operators](#bitwise-operators)
* [Most Important JVM Parameters](#most-important-jvm-parameters)
* [Java 8 features](#java-8-features)
* [Java 9 features](#java-9-features)
* [Java 10 features](#java-10-features)
* [Java 11 features](#java-11-features)
* [Java 12 features](#java-12-features)
* [Java 14 features](#java-14-features)
* [Java 15 features](#java-15-features)
* [Java 16 features](#java-16-features)
* [Java 17 features](#java-17-features)
* [Java 21 features](#java-21-features)
* [Annotations](#annotations)
* [Java dynamic proxy: JDK and CGLIB](#java-dynamic-proxy)
* [Sneaky throws](#sneaky-throws)
* [Dead Code Elimination](#dead-code-elimination)

## Stack

Each Java thread has its own stack which is typically 1 MB. (64k is the least stack space allowed in JVM).

## BitSet
To store and manipulate arrays of bits, one might argue that we should use boolean[] as our data structure. 
At first glance, that might seem a reasonable suggestion.
However, each boolean member in a boolean[] usually consumes one byte instead of just one bit.
[BitSet](https://www.baeldung.com/java-bitset)

```java
// This will create a BitSet instance with a long[] of size one. Of course, it can automatically grow this array if needed.
BitSet bitSet = new BitSet();
// Here, the internal array will have enough elements to hold 100,000 bits.
BitSet bitSet = new BitSet(100_00);
bitSet.set(10);
bitSet.set(20, 30, false);
bitSet.clear(42);
bitSet.clear(); // clear all bitset
bitSet.get(42);
bitSet.flip(42);
```

## Check Integer overflow
Using Math:
+ `toIntExact(long)`
+ `addExact(int,int)`
+ `subtractExact(int,int)`
+ `multiplyExact(int,int)`

convert to long and compare result
```java
long test = (long)x+y;
if (test > Integer.MAX_VALUE || test < Integer.MIN_VALUE)
   // Overflow!
```




## JVM object sizes
add
```xml
<dependency>
    <groupId>org.openjdk.jol</groupId>
    <artifactId>jol-core</artifactId>
    <version>0.10</version>
</dependency>
```

```java
System.out.println(VM.current().details());
/*
# Running 64-bit HotSpot VM.
# Using compressed oop with 3-bit shift.
# Using compressed klass with 3-bit shift.
# Objects are 8 bytes aligned.
# Field sizes by type: 4, 1, 1, 2, 2, 4, 4, 8, 8 [bytes]
# Array element sizes: 4, 1, 1, 2, 2, 4, 4, 8, 8 [bytes]  
 */
```
In the first few lines, we can see some general information about the VM. After that, we learn about object sizes:

+ Java references consume 4 bytes, booleans/bytes are 1 byte, chars/shorts are 2 bytes, ints/floats are 4 bytes, and finally, longs/doubles are 8 bytes
+ These types consume the same amount of memory even when we use them as array elements
+ So, in the presence of compressed references, each boolean value takes 1 byte. Similarly, each boolean in a boolean[] consumes 1 byte. However, alignment paddings and object headers can increase the space consumed by boolean and boolean[] as we’ll see later.

```java
class BooleanWrapper {
    private boolean value;
}

System.out.println(ClassLayout.parseClass(BooleanWrapper.class).toPrintable());
/*
OFFSET  SIZE      TYPE DESCRIPTION                               VALUE
   0    12           (object header)                           N/A
   12     1   boolean BooleanWrapper.value                      N/A
   13     3           (loss due to the next object alignment)
Instance size: 16 bytes
Space losses: 0 bytes internal + 3 bytes external = 3 bytes total
 */
```

The BooleanWrapper layout consists of:

+ 12 bytes for the header, including two mark words and one klass word. The HotSpot JVM uses the mark word to store the GC metadata, identity hashcode and locking information. Also, it uses the klass word to store class metadata such as runtime type checks
+ 1 byte for the actual boolean value
+ 3 bytes of padding for alignment purposes

By default, object references should be aligned by 8 bytes. 
Therefore, the JVM adds 3 bytes to 13 bytes of header and boolean to make it 16 bytes.

```java
boolean[] value = new boolean[3];
System.out.println(ClassLayout.parseInstance(value).toPrintable());
```

## Bitwise Operators 
Bitwise operators work on binary digits or bits of input values. 
We can apply these to the integer types –  long, int, short, char, and byte.
**bitwise operators always evaluate both operands.**
Bitwise operators work on a binary equivalent of decimal numbers and perform operations on them bit by bit as per the given operator:

Finally, the result `0111` will be converted back to decimal which is equal to `7`:
```java
/*
0110
0101
-----
0111
 */
int result = 6 | 5;
```

+ `OR (|)` - gives back 1 if either of them is 1.
+ `AND (&)` - gives back 1 if both are 1, otherwise it returns 0.
+ `XOR (^)` - gives back 1 if both the compared bits are different.
+ `COMPLEMENT (~)` - negation of each bit of the input value. It takes only one integer and it’s equivalent to the ! operator. 

Bitwise Operator Table
```
A	B	A|B	A&B	A^B	~A
0	0	0	0	0	1
1	0	1	0	1	0
0	1	1	0	1	1
1	1	1	1	0	0
```

Bitwise Shift Operators

+ `Signed Left Shift [<<]` - shifts the bits to the left by the number of times specified by the right side of the operand. After the left shift, the empty space in the right is filled with 0.
shifting a number by one is equivalent to multiplying it by 2, or, in general, **left shifting a number by n positions is equivalent to multiplication by 2^n**.
```java
int value = 12;
int leftShift = value << 2;
assertEquals(48, leftShift);
```

+ `Signed Right Shift [>>]` - The right shift operator shifts all the bits to the right. The empty space in the left side is filled depending on the input number:
+ When an input number is negative, where the leftmost bit is 1, then the empty spaces will be filled with 1
+ When an input number is positive, where the leftmost bit is 0, then the empty spaces will be filled with 0

+ `Unsigned Right Shift [>>>]` - The only difference is that the empty spaces in the left are filled with 0 irrespective of whether the number is positive or negative. Therefore, the result will always be a positive integer.


## Most Important JVM Parameters

+ [JVM options](#jvm-options)
+ [Minimal and maximal heap size](#minimal-and-maximal-heap-size)
+ [GC](#gc)
+ [Others](#others)

### JVM options
+ `-Dfile.encoding=UTF-8`
+ `-Djava.io.tmpdir=/mydir/`
+ `java -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=8000 OurApplication` remote debug

### Minimal and maximal heap size.
```shell
-Xms<heap size>[unit] 
-Xmx<heap size>[unit]
```
Here, unit denotes the unit in which we’ll initialize the memory (indicated by heap size). We can mark units as ‘g’ for GB, ‘m’ for MB, and ‘k’ for KB.

As per the Oracle guidelines, after total available memory, the second most influential factor is the proportion of the heap reserved for the Young Generation. By default, the minimum size of the YG is 1310 MB, and maximum size is unlimited.

We can assign them explicitly:
```shell
-XX:NewSize=<young size>[unit] 
-XX:MaxNewSize=<young size>[unit]
```

### GC
For better stability of the application, choosing the right Garbage Collection algorithm is critical.
JVM has four types of GC implementations:

+ Serial Garbage Collector
+ Parallel Garbage Collector
+ CMS Garbage Collector
+ G1 Garbage Collector
We can declare these implementations with the below parameters:
```shell
-XX:+UseSerialGC
-XX:+UseParallelGC
-XX:+USeParNewGC
-XX:+UseG1GC
```

To strictly monitor the application health, we should always check the JVM’s Garbage Collection performance. The easiest way to do this is to log the GC activity in human readable format.

Using the following parameters, we can log the GC activity:
```shell
-XX:+UseGCLogFileRotation 
-XX:NumberOfGCLogFiles=< number of log files > 
-XX:GCLogFileSize=< file size >[ unit ]
-Xloggc:/path/to/gc.log
```
+ `UseGCLogFileRotation` specifies the log file rolling policy, much like log4j, s4lj, etc. 
+ `NumberOfGCLogFiles` denotes the max number of log files we can write for a single application life cycle. 
+ `GCLogFileSize` specifies the max size of the file. Finally, loggc denotes its location.

One point to note here is that there are two more JVM parameters available (`-XX:+PrintGCTimeStamps` and `-XX:+PrintGCDateStamps`) that we can use to print date-wise timestamps in the GC log.

It’s very common for a large application to face an out of memory error, which in turn results in an application crash. It’s a very critical scenario, and very hard to replicate to troubleshoot the issue.

That’s why JVM comes with some parameters to dump heap memory into a physical file that we can use later to find leaks:
```shell
-XX:+HeapDumpOnOutOfMemoryError 
-XX:HeapDumpPath=./java_pid<pid>.hprof
-XX:OnOutOfMemoryError="< cmd args >;< cmd args >" 
-XX:+UseGCOverheadLimit
```

+ `HeapDumpOnOutOfMemoryError` instructs the JVM to dump heap into a physical file in case of OutOfMemoryError.
+ `HeapDumpPath` denotes the path where the file will be written. Any filename can be given; however, if JVM finds a <pid> tag in the name, the process id of the current process causing the out of memory error will be appended to the file name with .hprof format.
+ `OnOutOfMemoryError` is used to issue emergency commands that will be executed in case of an out of memory error. We should use proper commands in the space of cmd args. 
For example, if we want to restart the server as soon as an out of memory occurs, we can set the parameter: `-XX:OnOutOfMemoryError="shutdown -r"`
+ `UseGCOverheadLimit` is a policy that limits the proportion of the VM’s time that’s spent in GC before an OutOfMemory error is thrown.

[Tuning Garbage Collection with Oracle JDK](https://docs.oracle.com/cd/E40972_01/doc.70/e40973/cnf_jvmgc.htm#autoId1)

### Others
+ `-server`: enables “Server Hotspot VM.” We use this parameter by default in 64 bit JVM.
+ `-XX:+UseStringDeduplication`: Java 8u20 has introduced this JVM parameter for reducing the unnecessary use of memory by creating too many instances of the same String. This optimizes the heap memory by reducing duplicate String values to a single global char[] array.
+ `-XX:+UseLWPSynchronization`: sets a LWP (Light Weight Process) based synchronization policy instead of thread-based synchronization.
+ `-XX:LargePageSizeInBytes`: sets the large page size used for the Java heap. It takes the argument in GB/MB/KB. With larger page sizes, we can make better use of virtual memory hardware resources; however, this may cause larger space sizes for the PermGen, which in turn can force us to reduce the size of the Java heap space.
+ `-XX:MaxHeapFreeRatio`: sets the maximum percentage of heap free after GC to avoid shrinking
+ `-XX:MinHeapFreeRatio`: sets the minimum percentage of heap free after GC to avoid expansion. To monitor the heap usage, we can use VisualVM shipped with JDK.
+ `-XX:SurvivorRatio`: Ratio of eden/survivor space size. For example, -XX:SurvivorRatio=6 sets the ratio between each survivor space and eden space to be 1:6.
+ `-XX:+UseLargePages`: use large page memory if the system supports it. Please note that OpenJDK 7 tends to crash if using this JVM parameter.
+ `-XX:+UseStringCache`: enables caching of commonly allocated strings available in the String pool
+ `-XX:+UseCompressedStrings`: use a byte[] type for String objects which can be represented in pure ASCII format
+ `-XX:+OptimizeStringConcat`: it optimizes String concatenation operations where possible


## Java 8 features

* [Lambda Expression](#lambda-expression)
* [Functional Interface](#functional-interface)
* [Default Methods](#default-methods)
* [Predicate](#predicate)
* [Streams](#streams)

### Lambda Expression
A lambda is an anonymous function in Java. That is, a function which does not have a name, return type and access modifiers. 
Lambda expressions are also known as the anonymous functions or closures.

### Functional Interface
If an interface has one and only one abstract method, then it is called as a function interface.
```
    @FunctionalInterface
    Public interface A {
        Void myMethod();
    }
```

### Default Methods
Starting from Java 8, we are allowed to have default method implementations inside interfaces.
```
    public interface A {
        default void m1() {
            SOP(“m1 inside A”);
        }
    }
```
### Predicate
A predicate is a function with a single argument, and it returns a Boolean value.

### Streams
Using Streams, we can process the data in a declarative manner

## Java 9 features

*[Instance Private Methods and Static private Methods in Interface](#instance-private-methods-and-static-private-methods-in-interface)
*[Improved try with resource Blocks](#improved-try-with-resource-blocks)
*[Immutable Collection](#immutable-collection)
*[Modules](#modules)

### Instance Private Methods and Static private Methods in Interface
Starting from Java 9, we can create private methods in an Interface 
and can also use that private method inside of the default methods so that they can reuse certain functionality

### Improved try with resource Blocks
But starting from java 9, we can define our resources anywhere in our code which are auto closable then we can use them 
directly in the try block like the below:
```
    public static void main(String[] args) {
        MyThread th = new MyThread();
        try (th) {
            Th.executeSomething();
        } catch (Exception ex) {}
    }
```
### Immutable Collection
```
List list = List.of(“Sun”, “Moon”);
```
### Modules
Java 9 introduces java platform module system. A module is a bunch of packages.

## Java 10 features

* [Using Var keyword to declare variable](#using-var-keyword-to-declare-variable)

### Using Var keyword to declare variable
```
var x = 10;
```

## Java 11 features

* [String API Updates](#string-api-updates)
* [File API Updates](#file-api-updates)
* [IsEmpty() method on Optional Class](#isempty-method-on-optional-class)
* [Deprecations and Removals](#deprecations-and-removals)

### String API Updates
* `IsBlank()`: method
* `lines()`: This new method in Java 11 returns a stream of Strings by splitting the string using the new line character
```
String str = "I\nam\nthe\ncreator of this Blog";
System.out.println(str.lines().collect(Collectors.toList());
```
* `strip` method() The existing trim method does not have Unicode support. 
There are two variations of strip method i.e, `stripLeading()` method and another one is `stripTrailing()`.
* `repeat()` method.
```
System.out.println("-".repeat(100));
```

### File API Updates
Java 11 makes it super easy to read and write strings to and from a file.

`writeString`
```
Path path = Files.writeString(Files.createTempFile("test", ".txt"), "Java 11 features");
```
`readString`
```
String str = Files.readString(path);
```

### IsEmpty() method on Optional Class

### Deprecations and Removals
Like every other version, there are some deprecations and removals in Java 11. 
Java 11 has removed certain packages like java.xml.ws, java.activation, java.transaction, java.xml.bind and java.corba. 
All these packages and classes were part of the JDK earlier.

## Java 12 features
* [String Class New Methods](#string-class-new-methods)
* [File::mismatch](#filemismatch)

### String Class New Methods

* `indent` adjusts the indentation of each line based on the integer parameter. 
If the parameter is greater than zero, new spaces will be inserted at the beginning of each line. 
On the other hand, if the parameter is less than zero, it removes spaces from the begging of each line. 
If a given line does not contain sufficient white space, then all leading white space characters are removed.
* `transform` It accepts a single argument function as a parameter that will be applied to the string.

### File::mismatch
The method is used to compare two files and find the position of the first mismatched byte in their contents.

## Java 14 features

* [Switch Expressions](#switch-expressions)
* [Text Blocks](#text-blocks)
* [Pattern Matching](#pattern-matching-for-instanceof)
* [Records](#records)

### Switch Expressions
These were first introduced as a preview feature in JDK 12, and even in Java 13, they continued as preview features only. 
But now, switch expressions have been standardized so that they are part and parcel of the development kit.
Prior to this enhancement, we’d have written it as:
```
boolean isTodayHoliday;
switch (day) {
    case "MONDAY":
    case "TUESDAY":
    case "WEDNESDAY":
    case "THURSDAY":
    case "FRIDAY":
        isTodayHoliday = false;
        break;
    case "SATURDAY":
    case "SUNDAY":
        isTodayHoliday = true;
        break;
    default:
        throw new IllegalArgumentException("What's a " + day);
}
```
With switch expressions, we can write the same thing more succinctly:
```
boolean isTodayHoliday = switch (day) {
    case "MONDAY", "TUESDAY", "WEDNESDAY", "THURSDAY", "FRIDAY" -> false;
    case "SATURDAY", "SUNDAY" -> true;
    default -> throw new IllegalArgumentException("What's a " + day);
};
```

### Text Blocks
Text blocks continue their journey to getting a mainstream upgrade and are still available as preview features.
```
String multiline = """
    A quick brown fox jumps over a lazy dog; \
    the lazy dog howls loudly.""";
```

### Pattern Matching for instanceof
(preview)

before:
```
if (obj instanceof String) {
    String str = (String) obj;
    int len = str.length();
    // ...
}
```
now:
```
if (obj instanceof String str) {
    int len = str.length();
    // ...
}
```
### Records
(preview)
Records were introduced to reduce repetitive boilerplate code in data model POJOs. 
They simplify day to day development, improve efficiency and greatly minimize the risk of human error.

```
public record User(int id, String password) { };
```
This simple declaration will automatically add a constructor, getters, equals, hashCode and toString methods for us.
Records are immutable – once a record is created, you can't change its state.

### Helpful NullPointerExceptions
before:
```
Exception in thread "main" java.lang.NullPointerException
at com.baeldung.MyClass.main(MyClass.java:27)
```
now:
```
java.lang.NullPointerException: Cannot store to int array because "a" is null
```

## Java 15 features

* [Records](#records)
* [Sealed Classes](#sealed-classes)

### Records
While records eliminate a lot of boilerplate code, they do allow us to override some of the default behaviors. 
For example, we could define a canonical constructor that does some validation:
```
public record Person(String name, int age) {
    public Person {
        if(age < 0) {
            throw new IllegalArgumentException("Age cannot be negative");
        }
    }
}
```

### Sealed Classes
Currently, Java provides no fine-grained control over the inheritance. 
Access modifiers such as public, protected, private, as well as the default package-private, provide very coarse-grained control.
To that end, the goal of sealed classes is to allow individual classes to declare which types may be used as sub-types. 
This also applies to interfaces and determining which types can implement them.

Sealed classes involve two new keywords — sealed and permits:
```
public abstract sealed class Person
    permits Employee, Manager {
 
    //...
}
```
It’s important to note that any class that extends a sealed class must itself be declared sealed, non-sealed, or final. 
This ensures the class hierarchy remains finite and known by the compiler.
```
public final class Employee extends Person {
}

public non-sealed class Manager extends Person {
}
```

## Java 16 features

* [Invoke Default Methods From Proxy Instances](#invoke-default-methods-from-proxy-instances)
* [Add Stream.toList](#add-streamtolist)

### Invoke Default Methods From Proxy Instances
### Add Stream.toList
```
List<String> integersAsString = Arrays.asList("1", "2", "3");
List<Integer> ints = integersAsString.stream().map(Integer::parseInt).collect(Collectors.toList());
List<Integer> intsEquivalent = integersAsString.stream().map(Integer::parseInt).toList();
```

## Java 17 features

* [Sealed Classes](#sealed-classes)
* [Java Records](#java-records)
* [Helpful Null Pointers](#helpful-null-pointers)

### Sealed Classes
### Java Records
### Helpful Null Pointers

## Java 21 features

* [Pattern matching](#pattern-matching)
* [Unnamed Patterns and Variables](#unnamed-patterns-and-variables)

### Pattern matching
Java Introduced record type in Java 14. 
In version 21, Java introduced record patterns that de-structures the instances of record classes and enable more sophisticated data queries.
Record patterns were included as a preview feature in Java 19 and further refined in Java 20. 
Java 21 delivers some major changes of this feature.
```
// Record Instantiation
Point p = new Point(1, 2);
ColoredPoint coloredPoint = new ColoredPoint(p, Color.RED);
RandomPoint randomPoint = new RandomPoint(coloredPoint);

// Accessing the Point variables with nested record pattern

void nestedPrint(Object object) {
  if(object instanceof RandomPoint(ColoredPoint(Point(int x, int y), Color c))) {
    System.out.printf("X is %d, y is %d, Color is %s%n", x, y, c.name());
  }
}
```
The components must match, or the compiler won’t be happy:
```
record Point(int x, int y) { }

if (obj instanceof Point(long x, int y)) {
  // ...
}

// Error:
// incompatible types: pattern of type long is not applicable at int
// if (obj instanceof Point(long x, int y)) {
//                          ^----^
```
We can also use switch expressions to do specific actions based on our object types:
```
Double result = switch (object) {
    case Location(var name, GPSPoint(var latitude, var longitude)) -> latitude;
    default -> 0.0;
};
```

### Unnamed Patterns and Variables
(preview)
This feature improves readability throughout our code by allowing us to replace an unused variable with _ (underscore). 
No more `@SuppressWarnings("unused")`
```
try {
  //...
} catch (Exception _) {
  // we don't need the actual exception
}

int acc = 0;
for (Order _ : orders) {
    if (acc < LIMIT) { 
      // the actual order is not used
    }
}
```

## Annotations
Java annotations are a mechanism for adding metadata information to our source code.
* [Creating](#creating)
* [Applying annotations](#applying-annotations)
* [Processing Annotations](#processing-annotations)

### Creating
```
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.Type)
public @interface JsonSerializable {
}
```
As we can see, our first annotation has runtime visibility, and we can apply it to types (classes). 
Moreover, it has no methods, and thus serves as a simple marker to mark classes that can be serialized into JSON.
In the same fashion, we create our second annotation to mark the fields that we are going to include in the generated JSON:
```
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
public @interface JsonElement {
    public String key() default "";
}
```
When creating custom annotations with methods, we should be aware that these **methods must have no parameters, 
and cannot throw an exception. Also, the return types are restricted to primitives**, 
String, Class, enums, annotations, and arrays of these types, and the default value cannot be null.

### Applying annotations
```
@JsonSerializable
public class Person {

    @JsonElement
    private String firstName;
```

### Processing Annotations
The first step will be to check whether our object is null or not, 
as well as whether its type has the @JsonSerializable annotation or not:
```
private void checkIfSerializable(Object object) {
    if (Objects.isNull(object)) {
        throw new JsonSerializationException("The object to serialize is null");
    }
        
    Class<?> clazz = object.getClass();
    if (!clazz.isAnnotationPresent(JsonSerializable.class)) {
        throw new JsonSerializationException("The class " 
          + clazz.getSimpleName() 
          + " is not annotated with JsonSerializable");
    }
}
```
Processing
```
private String getJsonString(Object object) throws Exception {	
    Class<?> clazz = object.getClass();
    Map<String, String> jsonElementsMap = new HashMap<>();
    for (Field field : clazz.getDeclaredFields()) {
        field.setAccessible(true);
        if (field.isAnnotationPresent(JsonElement.class)) {
            jsonElementsMap.put(getKey(field), (String) field.get(object));
        }
    }		
     
    String jsonString = jsonElementsMap.entrySet()
        .stream()
        .map(entry -> "\"" + entry.getKey() + "\":\"" 
          + entry.getValue() + "\"")
        .collect(Collectors.joining(","));
    return "{" + jsonString + "}";
}
```

## Java dynamic proxy

Java dynamic proxy is widely used in a variety of applications from Spring AOP, RPC to the mapper used in mybatis. 
A typical example of using Java dynamic proxy is the situation where you want to add some custom add-on functions 
saying parameter verification on the existing class but you don’t want to modify the exiting class.

* **JDK Dynamic** proxy can only proxy by interface (so your target class needs to implement an interface, which is then also implemented by the proxy class).
* **CGLIB** (and javassist) can create a proxy by subclassing. In this scenario the proxy becomes a subclass of the target class. No need for interfaces.

* [JDK](#jdk)
* [CGLIB](#cglib)

### JDK
One is implemented by using Java JDK.

There two critical classes: **Proxy** and **InvocationHandler**

* Proxy: It provides you a static factory method to create a proxy instance on which you make a call. The proxy could delegate your call to the real class
* InvocationHandler: It defines an invoke method which will be triggered when you make a call on the proxy instance.

```
@Data
public class User {
    private String name;
}
public interface UserService {
    void addUser(User user);
}
public class UserServiceImpl implements UserService {
    @Override
    public void addUser(User user) {
        System.out.println(user.getName() + " is added successfully");
    }
}
```

```
public class UserServiceJdkInterceptor implements InvocationHandler {
    private Object realObj;

    public UserServiceJdkInterceptor(Object realObject) {
        super();
        this.realObj = realObject;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        Object result = null;
        if (args != null && args.length > 0 && args[0] instanceof User) {
            User user = (User)args[0];
            System.out.println("we are in UserServiceJdkInterceptor. " + user.getName() + " will be added");
            result = method.invoke(realObj, args);
        }
        return result;
    }
}
```
Let’s write a client to run UserServiceJdkInterceptor:
```
public class ClientTest {
    public static void main(String[] args) {
        UserService target = new UserServiceImpl(); // The real object
        InvocationHandler interceptor = new UserServiceJdkInterceptor(target);
        UserService proxy = (UserService) Proxy.newProxyInstance(target.getClass().getClassLoader(), target.getClass().getInterfaces(), interceptor);

        User user = new User();
        user.setName("JanessaTech2022");
        proxy.addUser(user);
    }
}
```

### CGLIB

CGLIB is the abbreviation of Code Generation Libary. The usage is similar to the previous one. 
There are two critical classes: **Enhancer** and **MethodInterceptor**

Enhancer: Similar to the Proxy in the JDK way. It uses create() method to create a proxy instance on which you make a call

MethodInterceptor: Similar to InvocationHandler in the JDK way. 
The intercept() method will be called when you make a call on the proxy instance

```
import com.janessa.dynamicProxy.common.User;
import org.springframework.cglib.proxy.MethodInterceptor;
import org.springframework.cglib.proxy.MethodProxy;

import java.lang.reflect.Method;

public class UserServiceCglibInterceptor implements MethodInterceptor {
    private Object realObj;

    public UserServiceCglibInterceptor(Object realObject) {
        super();
        this.realObj = realObject;
    }

    @Override
    public Object intercept(Object o, Method method, Object[] args, MethodProxy proxy) throws Throwable {
        Object result = null;
        if (args != null && args.length > 0 && args[0] instanceof User) {
            User user = (User)args[0];
            System.out.println("we are in UserServiceCglibInterceptor. " + user.getName() + " will be added");
            result = method.invoke(realObj, args);
        }
        return result;
    }
}
```


For JDK implememtation:
1. we don’t need extra dependence 
2. final key word on method doesn’t effect the execution of add-on function

For CGLIB implememtation: 
1. we need extra dependence 
2. final key word on method will diable the execution of add-on function

## Sneaky throws
In Java, the sneaky throw concept allows us to throw any checked exception without defining it explicitly in the method signature. 
This allows the omission of the throws declaration, effectively imitating the characteristics of a runtime exception.
Checked exceptions are part of Java, not the JVM. In the bytecode, we can throw any exception from anywhere, without restrictions.

Java 8 brought a new type inference rule that states that a throws T is inferred as RuntimeException whenever allowed. 
This gives the ability to implement sneaky throws without the helper method.

A problem with sneaky throws is that you probably want to catch the exceptions eventually, 
but the Java compiler doesn’t allow you to catch sneakily thrown checked exceptions using exception handler for their particular exception type.

```
public static <E extends Throwable> void sneakyThrow(Throwable e) throws E {
    throw (E) e;
}

private static void throwSneakyIOException() {
    sneakyThrow(new IOException("sneaky"));
}
```
The @SneakyThrows annotation from Lombok allows you to throw checked exceptions without using the throws declaration. 
This comes in handy when you need to raise an exception from a method within very restrictive interfaces like Runnable.

## Dead Code Elimination
Dead Code Elimination is a compiler optimization technique which identifies and removes code that is not reachable or does not affect the program's output.

```
import org.openjdk.jmh.annotations.*;
import java.util.concurrent.TimeUnit;

@State(Scope.Thread)
@BenchmarkMode(Mode.AverageTime)
@OutputTimeUnit(TimeUnit.NANOSECONDS)
@Fork(1)
public class Sample08DeadCode {

    private double x = Math.PI;

    @Benchmark
    public void benchmark() {}

    @Benchmark
    public void measureIncorrect() { Math.log(x); }

    @Benchmark
    public double measureCorrect() { return Math.log(x); }
}
```

Run using JDK 1.8.0_211, Java HotSpot(TM) 64-Bit Server VM, 25.211-b12 produces following results:
```
Benchmark                          Mode  Cnt   Score   Error  Units
Sample08DeadCode.benchmark         avgt    5   0,229 ± 0,018  ns/op
Sample08DeadCode.measureCorrect    avgt    5  12,013 ± 0,047  ns/op
Sample08DeadCode.measureIncorrect  avgt    5   0,228 ± 0,016  ns/op
```