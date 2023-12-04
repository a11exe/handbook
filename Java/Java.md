# Java

* [Annotations](#annotations)
* [Java dynamic proxy: JDK and CGLIB](#java-dynamic-proxy)

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