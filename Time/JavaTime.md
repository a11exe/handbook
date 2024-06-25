# Java time

## Epoch

An epoch is a an absolute time reference. Most programming languages (e.g Java, JavaScript, Python)
use the Unix epoch (Midnight 1 January 1970) when expressing a given timestamp as the number of milliseconds
elapsed since a fixed point-in-time reference.

## Time zone

The coordinated universal time (UTC) is the most common time standard.
The UTC time zone (equivalent to GMT) represents the time reference all other time zones relate to (through a
positive/negative offset).

UTC time zone is commonly referred to as Zulu time (Z) or UTC+0. Japan’s time zone is UTC+9 and the Honolulu time zone
is UTC-10.

## ISO 8601

ISO 8601 is the most widespread date/time representation standard and it uses the following date/time formats:

| TIME ZONE     | NOTATION                      | 
|---------------|-------------------------------|
| UTC           | 1970-01-01T00:00:00.000+00:00 |
| UTC Zulu time | 1970-01-01T00:00:00.000Z      |
| Tokio         | 1970-01-01T09:00:00.000+09:00 |
| Honolulu      | 1969-12-31T14:00:00.000-10:00 |

## Relative vs Absolute time instances

### Relative timestamp
The numeric timestamp representation (the numbers of milliseconds since epoch) is relative info. 
This value is given against the UTC epoch but you still need a time zone to properly represent the actual time on a particular region.

If you don’t know the original event time zone, you risk displaying a timestamp against the current local time zone and this is not always desirable.

### Absolute timestamp
The absolute timestamp contains both the relative time as well as the time zone info. 
It’s quite common to express timestamps in their ISO 8601 string representation.

Absolute time representation is much more convenient when we want to reconstruct the time instance against the original time zone. 
An e-mail client might want to display the email creation date using the sender’s time zone, and this can only be achieved using absolute timestamps.

## Java date time processing
As illustrated in the above flow chart there are 3 stages that data transformation happening and similarly each time there is a time transformation happening. 
1 from the client-side and two from the server-side.

Rest API -> Jackson framework -> Persistence framework -> DB

### Jackson framework
[OffsetDateTime Serialization With Jackson](https://www.baeldung.com/java-jackson-offsetdatetime)

By default, ISO DataTime format will be deserialized in UTC format (as Instant).
`DeserializationFeature.ADJUST_DATES_TO_CONTEXT_TIME_ZONE` - option to deserialize with time offset. 
By default, this option enabled and date time will be adjusted to context time zone, which is by default UTC. 
It works as for Rest API deserialization and for kafka deserialization also.

```java
@Bean
    ObjectMapper getObjectMapper() {
        ObjectMapper objectMapper = new ObjectMapper();
        // some other config...
        objectMapper.disable(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS);
        JavaTimeModule javaTimeModule = new JavaTimeModule();
        objectMapper.registerModule(javaTimeModule);
        objectMapper.disable(DeserializationFeature.ADJUST_DATES_TO_CONTEXT_TIME_ZONE);
        return objectMapper;
    }
```

### Hibernate
We can see that Hibernate converted the timestamp according to our local time zone.
* [How to map Date and Timestamp with JPA and Hibernate](https://vladmihalcea.com/date-timestamp-jpa-hibernate/)
* [How to store date, time, and timestamps in UTC time zone with JDBC and Hibernate](https://vladmihalcea.com/how-to-store-date-time-and-timestamps-in-utc-time-zone-with-jdbc-and-hibernate/)
`OffsetDateTimeJavaDescriptor` wraps the provided `OffsetDateTime` to a `Timestamp`, like this:

```java
return (X) Timestamp.from( offsetDateTime.toInstant() );
```
And, when reading it from the database it converts it to the local time zone:
```java
return OffsetDateTime.ofInstant( ts.toInstant(), ZoneId.systemDefault() );
```

So timestamp will be in database according Local time zone.

This mapping works as long as:

* you use a time zone without daylight saving time,
* **all instances of your application use the same time zone**, and
* you never have to change this time zone.

To avoid it and save OffsetDateTime in database in UTC use option:
```java
spring.jpa.properties.hibernate.jdbc.time_zone: UTC
```

Saving
```java
                else if ( options.getJdbcTimeZone() != null ) {
					st.setTimestamp( index, timestamp, Calendar.getInstance( options.getJdbcTimeZone() ) );
				}
				else {
					st.setTimestamp( index, timestamp );
				}
```

Extraction
```java
@Override
			protected X doExtract(ResultSet rs, int paramIndex, WrapperOptions options) throws SQLException {
				return options.getJdbcTimeZone() != null ?
					javaType.wrap( rs.getTimestamp( paramIndex, Calendar.getInstance( options.getJdbcTimeZone() ) ), options ) :
					javaType.wrap( rs.getTimestamp( paramIndex ), options );
			}
```

## Storing offset in separate column
While in Hibernate 5, there was no built-in support for saving the OffsetDateTime ZoneOffset, 
since Hibernate 6, we can now use the TimeZoneColumn annotation to achieve this goal.
[How to map the OffsetDateTime ZoneOffset with Hibernate TimeZoneColumn](https://vladmihalcea.com/offsetdatetime-zoneoffset-hibernate-timezonecolumn/)

## Hibernate 6 changes
[TimezoneStorageType – Hibernate’s improved timezone mapping](https://thorben-janssen.com/hibernate-6-offsetdatetime-and-zoneddatetime/)

## Docker
If you are running databases on UTC (Docker default to UTC) is always possible for you to change the time zone with TZ environment variable, 
by default Docker container, configure to UTC. For other use cases if the DBs are running on a server machine it will take the local machine default time zone, 
but with most modern RDBMS you will be able to change time zone easily.
