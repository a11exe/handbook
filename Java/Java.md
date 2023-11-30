# Java

* [Annotations](#annotations)

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

