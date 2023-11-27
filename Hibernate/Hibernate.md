# Hibernate

* [Immutable](#immutable)
* [Converter](#converter)
* [Inheritance strategies](#inheritance-strategies)

### Immutable

```
@Entity
@Immutable
@Table(name = "events_generated")
public class EventGeneratedId
```

`@Immutable` annotation on Entity means we can insert or delete entity. 
If we try to update entity Hibernate will simply ignore the update operation without throwing an exception.

Immutable also can be used with collections.
```
@Immutable
public Set<String> getGuestList() {
    return guestList;
}
```
Adding or deleting will cause exception
```
org.hibernate.HibernateException: 
  changed an immutable collection instance: [com.baeldung.entities.Event.guestList#1]
```

### Converter
`@Converter` allow us to map JDBC types to Java classes.

Data class
```
public class PersonName implements Serializable {

    private String name;
    private String surname;

    // getters and setters
}
```
Entity
```
@Entity(name = "PersonTable")
public class Person {

    @Convert(converter = PersonNameConverter.class)
    private PersonName personName;
    
    // ...
}
```
Now we need to create a converter that transforms the PersonName attribute to a database column and vice-versa.
To do so we have to annotate our converter class with @Converter and implement the AttributeConverter interface. 
We’ll parametrize the interface with the types of the class and the database column, in that order.
Notice that we had to implement 2 methods: convertToDatabaseColumn() and convertToEntityAttribute().
```
@Converter
public class PersonNameConverter implements 
  AttributeConverter<PersonName, String> {

    private static final String SEPARATOR = ", ";

    @Override
    public String convertToDatabaseColumn(PersonName personName) {
        if (personName == null) {
            return null;
        }

        StringBuilder sb = new StringBuilder();
        if (personName.getSurname() != null && !personName.getSurname()
            .isEmpty()) {
            sb.append(personName.getSurname());
            sb.append(SEPARATOR);
        }

        if (personName.getName() != null 
          && !personName.getName().isEmpty()) {
            sb.append(personName.getName());
        }

        return sb.toString();
    }

    @Override
    public PersonName convertToEntityAttribute(String dbPersonName) {
        if (dbPersonName == null || dbPersonName.isEmpty()) {
            return null;
        }

        String[] pieces = dbPersonName.split(SEPARATOR);

        if (pieces == null || pieces.length == 0) {
            return null;
        }

        PersonName personName = new PersonName();        
        String firstPiece = !pieces[0].isEmpty() ? pieces[0] : null;
        if (dbPersonName.contains(SEPARATOR)) {
            personName.setSurname(firstPiece);

            if (pieces.length >= 2 && pieces[1] != null 
              && !pieces[1].isEmpty()) {
                personName.setName(pieces[1]);
            }
        } else {
            personName.setName(firstPiece);
        }

        return personName;
    }
}
```

### Inheritance strategies
JPA specification provides several strategies:

* **MappedSuperclass** – the parent classes, can’t be entities
* **Single Table** – The entities from different classes with a common ancestor are placed in a single table.
* **Joined Table** – Each class has its table, and querying a subclass entity requires joining the tables.
* **Table per Class** – All the properties of a class are in its table, so no join is required.

Entity inheritance means that we can use polymorphic queries for retrieving all the subclass entities when querying for a superclass.

#### MappedSuperclass
Using the MappedSuperclass strategy, inheritance is only evident in the class but not the entity model.

Let’s start by creating a Person class that will represent a parent class:
```
@MappedSuperclass
public class Person {

    @Id
    private long personId;
    private String name;

    // constructor, getters, setters
}
```
Notice that this class no longer has an @Entity annotation, as it won’t be persisted in the database by itself.
Next, let’s add an Employee subclass:
```
@Entity
public class MyEmployee extends Person {
    private String company;
    // constructor, getters, setters 
}
```
In the database, this will correspond to one MyEmployee table with three columns for the declared and inherited fields of the subclass.
If we’re using this strategy, ancestors cannot contain associations with other entities.

#### Single Table
The Single Table strategy creates one table for each class hierarchy. JPA also chooses this strategy by default if we don’t specify one explicitly.

We can define the strategy we want to use by adding the @Inheritance annotation to the superclass:
```
@Entity
@Inheritance(strategy = InheritanceType.SINGLE_TABLE)
public class MyProduct {
    @Id
    private long productId;
    private String name;

    // constructor, getters, setters
}
```
Then we can add the subclass entities:
```
@Entity
public class Book extends MyProduct {
    private String author;
}
```
```
@Entity
public class Pen extends MyProduct {
    private String color;
}
```
**Discriminator Values**
Since the records for all entities will be in the same table, Hibernate needs a way to differentiate between them.

By default, this is done through a discriminator column called DTYPE that has the name of the entity as a value.

To customize the discriminator column, we can use the @DiscriminatorColumn annotation:
```
@Entity(name="products")
@Inheritance(strategy = InheritanceType.SINGLE_TABLE)
@DiscriminatorColumn(name="product_type", 
  discriminatorType = DiscriminatorType.INTEGER)
public class MyProduct {
    // ...
}
```
Here we’ve chosen to differentiate MyProduct subclass entities by an integer column called product_type.

Next, we need to tell Hibernate what value each subclass record will have for the product_type column:
```
@Entity
@DiscriminatorValue("1")
public class Book extends MyProduct {
    // ...
}
```
Hibernate adds two other predefined values that the annotation can take — null and not null:
* @DiscriminatorValue(“null”) means that any row without a discriminator value will be mapped to the entity class with this annotation; this can be applied to the root class of the hierarchy.
* @DiscriminatorValue(“not null”) – Any row with a discriminator value not matching any of the ones associated with entity definitions will be mapped to the class with this annotation.

#### Joined Table
Using this strategy, each class in the hierarchy is mapped to its table. The only column that repeatedly appears in all the tables is the identifier, which will be used for joining them when needed.

Let’s create a superclass that uses this strategy:
```
@Entity
@Inheritance(strategy = InheritanceType.JOINED)
public class Animal {
    @Id
    private long animalId;
    private String species;

    // constructor, getters, setters 
}
```
Then we can simply define a subclass:
```
@Entity
public class Pet extends Animal {
    private String name;

    // constructor, getters, setters
}
```
Both tables will have an animalId identifier column.

The primary key of the Pet entity also has a foreign key constraint to the primary key of its parent entity.

The disadvantage of this inheritance mapping method is that retrieving entities requires joins between tables, 
which can result in lower performance for large numbers of records.

#### Table per Class
The Table per Class strategy maps each entity to its table, which contains all the properties of the entity, including the ones inherited.

The resulting schema is similar to the one using @MappedSuperclass. But Table per Class will indeed define entities for parent classes, allowing associations and polymorphic queries as a result.

To use this strategy, we only need to add the @Inheritance annotation to the base class:
```
@Entity
@Inheritance(strategy = InheritanceType.TABLE_PER_CLASS)
public class Vehicle {
    @Id
    private long vehicleId;

    private String manufacturer;

    // standard constructor, getters, setters
}
```
Then we can create the subclasses in the standard way.
This is not that different from merely mapping each entity without inheritance. The distinction is clear when querying the base class, which will return all the subclass records as well by using a UNION statement in the background.

The use of UNION can also lead to inferior performance when choosing this strategy. Another issue is that we can no longer use identity key generation.

