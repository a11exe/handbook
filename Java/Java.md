# Java

Java SE 8, 11, 17 and 21 are LTS releases

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