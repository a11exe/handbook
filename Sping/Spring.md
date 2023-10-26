# Spring

* [ShedLock](#shedlock)
* [Read only](#read-only)

## ShedLock
Spring provides an easy way to implement API for scheduling jobs. It works great until we deploy multiple instances of our application.

Spring, by default, cannot handle scheduler synchronization over multiple instances. It executes the jobs simultaneously on every node instead.
ShedLock — a Java library that makes sure our scheduled tasks run only once at the same time and is an alternative to Quartz.
[ShedLock](https://www.baeldung.com/shedlock-spring)

## Read only
Read intense applications can leverage those optimizations and save resource utilization on our database cluster.
In a more real-life scenario, we could have two data sources, a writer one and a read-only one. 
Then, we’d have to define which data source to use at the component level. 
This approach handles the read connections more efficiently and prevents the unnecessary commands used to ensure 
the session is clean and has the appropriate setup.

[Read only](https://www.baeldung.com/spring-transactions-read-only)