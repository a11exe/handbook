# Hibernate

* [Mapping a query to a custom class](#mapping-a-query-to-a-custom-class)
* [Read-Only Repository with Spring Data](#read-only-repository-with-spring-data)
* [Idempotent update](#idempotent-update)
* [Transaction Locks](#transaction-locks)
* [Query hints](#query-hints)
* [Show Hibernate/JPA SQL](#show-hibernatejpa-sql)
* [Immutable](#immutable)
* [Converter](#converter)
* [Json](#json)
* [Inheritance strategies](#inheritance-strategies)

### Mapping a query to a custom class
#### Customizing the Result With Class Constructors
The JPA specification allows us to customize results in an object-oriented fashion. 
Therefore, we can use a JPQL constructor expression to set the result.
This binds the output of the SELECT statement to a POJO.
```java
@Query("SELECT new com.baeldung.aggregation.model.custom.CommentCount(c.year, COUNT(c.year)) "
  + "FROM Comment AS c GROUP BY c.year ORDER BY c.year DESC")
List<CommentCount> countTotalCommentsByYearClass();
```
#### Customizing the Result With Spring Data Projection
Another possible solution is to customize the result of JPA queries with Spring Data Projection. 
This functionality allows us to project query results with considerably less code.

To use interface-based projection, we must define a Java interface composed of getter methods that match 
the projected attribute names. Let’s define an interface for our query result:
```java
public interface ICommentCount {
    Integer getYearComment();
    Long getTotalComment();
}
```
Now let’s express our query with the result returned as List<ICommentCount>:
```java
@Query("SELECT c.year AS yearComment, COUNT(c.year) AS totalComment "
  + "FROM Comment AS c GROUP BY c.year ORDER BY c.year DESC")
List<ICommentCount> countTotalCommentsByYearInterface();
```
Spring Data will then construct the result on-the-fly, and return a proxy instance for each row of the result



### Read-Only Repository with Spring Data
It’s sometimes necessary to read data out of a database without having to modify it. 
In this case, having a read-only Repository interface would be perfect.
It’ll provide the ability to read data without the risk of anyone changing it.

CrudRepository actually extends another interface called Repository. We can also extend this interface to fit our needs.

Let’s create a new interface that extends Repository:
```java
@NoRepositoryBean
public interface ReadOnlyRepository<T, ID> extends Repository<T, ID> {
    Optional<T> findById(ID id);
    List<T> findAll();
}
```
Here, we’ve only defined two read-only methods. 
The entity that is accessed by this repository will be safe from any modification.
It is also important to note that we must use the @NoRepositoryBean annotation to tell Spring that 
we want this repository to remain generic. This allows us to reuse our read-only repository for as many different entities as we want.

Next, we’ll see how to tie an entity to our new ReadOnlyRepository
```
public interface BookReadOnlyRepository extends ReadOnlyRepository<Book, Long> {
    List<Book> findByAuthor(String author);
    List<Book> findByTitle(String title);
}
```

### Idempotent update
```java
@Modifying
@Query("update MyEntity e set e.status = :status where e.id = :id and e.status = :oldStatus")
int idempotentStatusUpdate(@Param("id") UUID id, @Param("status") Status status, @Param("oldStatus") Status oldStatus);
```

### Transaction Locks
JPA has two main lock types defined, Pessimistic Locking and Optimistic Locking.

[Pessimistic Locking](#pessimistic-locking)
[Optimistic Locking](#optimistic-locking)

#### Pessimistic Locking
When we use Pessimistic Locking in a transaction, and access an entity, it’ll be locked immediately. The transaction releases the lock either by committing or rolling back the transaction.
To specify a lock on a custom query method of a Spring Data JPA repository, we can annotate the method with @Lock and specify the required lock mode type:
```java
@Lock(LockModeType.PESSIMISTIC_READ)
public Optional<Customer> findById(Long customerId);
```

```java
@Lock(LockModeType.PESSIMISTIC_READ)
@QueryHints({@QueryHint(name = "jakarta.persistence.lock.timeout", value = "3000")})
public Optional<Customer> findById(Long customerId);
```
In JPA, achieve **SELECT FOR UPDATE** by using LockModeType of **PESSIMISTIC_WRITE** during a query


#### Optimistic Locking
In Optimistic Locking, the transaction doesn’t lock the entity immediately. Instead, the transaction commonly saves the entity’s state with a version number assigned to it.
When we try to update the entity’s state in a different transaction, the transaction compares the saved version number with the existing version number during the update.
At this point, if the version number differs, it means that we can’t modify the entity. If there’s an active transaction, then that transaction will be rolled back and the underlying JPA implementation will throw an OptimisticLockException.
```java
@Lock(LockModeType.OPTIMISTIC_FORCE_INCREMENT)
@Query("SELECT c FROM Customer c WHERE c.orgId = ?1")
public List<Customer> fetchCustomersByOrgId(Long orgId);
```

### Query hints
Hibernate is tracking all objects loaded within the session to find the modifications and persist all the changes when you flush the session. 
If you load the entity as read-only, you instruct Hibernate not to trace that entity for changes. 
In that way, you will get some performance increase.
```java
@QueryHints(value = {
    @QueryHint(name = HINT_FETCH_SIZE, value = "3000"),
    @QueryHint(name = HINT_CACHABLE, value = "false"),
    @QueryHint(name = READ_ONLY, value = "true")
})
@Query("from MyViewEntity")
Stream<MyEntity> findAllStream();
```

### Show Hibernate/JPA SQL
via properties
```
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
```
via loggers
```
logging.level.org.hibernate.SQL=DEBUG
logging.level.org.hibernate.type.descriptor.sql.BasicBinder=TRACE
```

### Immutable

```java
@Entity
@Immutable
@Table(name = "events_generated")
public class EventGeneratedId
```

`@Immutable` annotation on Entity means we can insert or delete entity. 
If we try to update entity Hibernate will simply ignore the update operation without throwing an exception.

Immutable also can be used with collections.
```java
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
```java
public class PersonName implements Serializable {

    private String name;
    private String surname;

    // getters and setters
}
```
Entity
```java
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
```java
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
### Json
#### Store json as varchar
Stored data:
```java
public class Address {
    private String postCode;

    private String city;

    // constructor, getters and setters
}
```
Enitiy
```java
@Entity
@Table(name = "student")
public class StudentEntity {
    @Id
    @Column(name = "student_id", length = 8)
    private String id;

    @Column(name = "admit_year", length = 4)
    private String admitYear;

    @Convert(converter = AddressAttributeConverter.class)
    @Column(name = "address", length = 500)
    private Address address;

    // constructor, getters and setters
}
```
It’s mandatory to define two conversion methods for every AttributeConverter implementation. 
One converts the Java data type to its corresponding database data type, while the other converts the database data type to the Java data type
```java
@Converter
public class AddressAttributeConverter implements AttributeConverter<Address, String> {
    private static final ObjectMapper objectMapper = new ObjectMapper();

    @Override
    public String convertToDatabaseColumn(Address address) {
        try {
            return objectMapper.writeValueAsString(address);
        } catch (JsonProcessingException jpe) {
            log.warn("Cannot convert Address into JSON");
            return null;
        }
    }

    @Override
    public Address convertToEntityAttribute(String value) {
        try {
            return objectMapper.readValue(value, Address.class);
        } catch (JsonProcessingException e) {
            log.warn("Cannot convert JSON into Address");
            return null;
        }
    }
}
```

#### Store json as jsonb
JSONB type enforces data validation on the stored value that makes sure the column value is a valid JSON. 
PostgreSQL rejects any attempts to insert or update data with invalid JSON values.
Quering using json data:
```java
@Repository
public interface StudentRepository extends CrudRepository<StudentEntity, String> {
    @Query(value = "SELECT * FROM student WHERE address->>'postCode' = :postCode", nativeQuery = true)
    List<StudentEntity> findByAddressPostCode(@Param("postCode") String postCode);
```
Indexing
```sql
CREATE INDEX idx_postcode ON student USING HASH((address->'postCode'));
```
We have to add dependency:
```
<dependency>
    <groupId>io.hypersistence</groupId>
    <artifactId>hypersistence-utils-hibernate-55</artifactId>
    <version>3.7.0</version>
</dependency>
```
Entity:
```java
@Entity
@Table(name = "student")
@TypeDef(name = "jsonb", typeClass = JsonBinaryType.class)
public class StudentEntity {
    @Id
    @Column(name = "student_id", length = 8)
    private String id;

    @Column(name = "admit_year", length = 4)
    private String admitYear;

    @Type(type = "jsonb")
    @Column(name = "address", columnDefinition = "jsonb")
    private Address address;

    // getters and setters
}
```
For this case of using @Type, we don’t need to apply the AttributeConverter to the address field anymore.

### Inheritance strategies
JPA specification provides several strategies:

* [**MappedSuperclass**](#mappedsuperclass) – the parent classes, can’t be entities
* [**Single Table**](#single-table) – The entities from different classes with a common ancestor are placed in a single table.
* [**Joined Table**](#joined-table) – Each class has its table, and querying a subclass entity requires joining the tables.
* [**Table per Class**](#table-per-class) – All the properties of a class are in its table, so no join is required.

Entity inheritance means that we can use polymorphic queries for retrieving all the subclass entities when querying for a superclass.

#### MappedSuperclass
Using the MappedSuperclass strategy, inheritance is only evident in the class but not the entity model.

Let’s start by creating a Person class that will represent a parent class:
```java
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
```java
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
```java
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
```java
@Entity
public class Pen extends MyProduct {
    private String color;
}
```
**Discriminator Values**
Since the records for all entities will be in the same table, Hibernate needs a way to differentiate between them.

By default, this is done through a discriminator column called DTYPE that has the name of the entity as a value.

To customize the discriminator column, we can use the @DiscriminatorColumn annotation:
```java
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
```java
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
```java
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
```java
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
```java
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

