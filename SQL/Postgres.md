# Postgres

* [Find config files](#find-config-files)
* [Create database and user](#create-database-and-user)
* [Analyze queries](#analyze-queries)
* [Find slow, long-running, and Blocked Queries](#find-slow-long-running-and-blocked-queries)
* [Partitioned tables](#partitioned-tables)
* [List indexes in Postgres](#list-indexes-in-postgres)
* [Using indexes](#using-indexes)
* [Why Is My Query Not Using an Index?](#why-is-my-query-not-using-an-index)
* [Partial Indexes](#partial-indexes)
* [Expression Indexes](#expression-indexes)
* [Unique Indexes](#using-indexes)
* [Multi-Column Indexes](#multi-column-indexes)
* [B-trees and Sorting](#b-trees-and-sorting)
* [Managing and Maintaining Indexes](#managing-and-maintaining-indexes)

### Find config files
```
sudo -u postgres psql -d postgres
postgres=# SHOW hba_file;
```

### Create database and user
```
sudo -u postgres psql
postgres=# create database mydb;
postgres=# create user myuser with encrypted password 'mypass';
postgres=# grant all privileges on database mydb to myuser;
```
Doing purely via psql
```
CREATE DATABASE yourdbname;
CREATE USER youruser WITH ENCRYPTED PASSWORD 'yourpass';
GRANT ALL PRIVILEGES ON DATABASE yourdbname TO youruser;
```

### Analyze queries

The pg_stat_statements module provides a means for tracking planning and execution statistics of all SQL statements executed by a server.
The pg_stat_statements module must be loaded by adding pg_stat_statements to shared_preload_libraries in postgresql.conf, because it requires additional shared memory.
#### Modify PostgreSQL Configuration
To enable pg_stat_statements, you need to modify the following PostgreSQL configuration in PostgreSQL configuration file (e.g. /etc/postgresql/12/main/postgresql.conf):

```
shared_preload_libraries = 'pg_stat_statements'
pg_stat_statements.track = all
```

#### Restart PostgreSQL
After you change the PostgreSQL configuration, **you need to restart PostgreSQL** to make the change effective.
Currently, pg_stat_statements only tracks the statistics of the database where the extension is created. So you need to create the extension for each database.

```
CREATE EXTENSION IF NOT EXISTS pg_stat_statements;
```
#### Check pg_stat_statements
```
SELECT count(*) FROM pg_stat_statements;
```
To access and manipulate these statistics, the module provides views pg_stat_statements and pg_stat_statements_info, 
and the utility functions pg_stat_statements_reset and pg_stat_statements. 
These are not available globally but can be enabled for a specific database with CREATE EXTENSION pg_stat_statements.
Example
```
SELECT substring(query, 1, 50) AS short_query,
              round(total_exec_time::numeric, 2) AS total_time,
              calls,
              round(mean_exec_time::numeric, 2) AS mean,
              round((100 * total_exec_time / sum(total_exec_time::numeric) OVER ())::numeric, 2) AS percentage_cpu
FROM  pg_stat_statements
ORDER BY total_time DESC
LIMIT 20;
```

### Find slow, long-running, and Blocked Queries
* `pg_stat_activity`: A table with one entry per server process, showing details of the running query for each.
* `pg_locks`: Information on current locks held within the database by open transactions, with one row per lockable object.
* `pg_blocking_pids()`: A function that can find the process IDs (PIDs) of sessions that are blocking the PostgreSQL server process of a supplied PID.
* `pg_cancel_backend()`: Function that cancels the currently running query by sending a SIGINT to a process ID.
* `pg_terminate_backend()`: Terminate a backend process completely (the query and usually the connection) on the database (uses SIGTERM instead of SIGINT).

A simple `select * from pg_stat_activity` will provide a snapshot of what is happening on your PostgreSQL database, with one line per current transaction, and the key columns:

* `datname`: The database name that the query is running on.
* `pid`: The Process ID of the backend for the running query. PID can be used with pg_terminate_backend() or pg_cancel_backend() separately.
* `usename`: user name that is running/connected to this backend
* `client_addr`: IP address of the connection to this backend. If this is empty/null, the row may refer to an internal connection or internal process.
* `backend_start`: Time when the backend process was started, or when the client connected.
* `query_start`: Time when the currently active query was started.
* `state`: The current status of the backend, with options:
  * `active`: currently executing the query in the query column.
  * `idle`: not executing anything, and waiting for a new command.
  * `idle in transaction`: the backend is in a transaction, but not currently doing any work. This is the same as with “idle in transaction (error)” except one of the statements has an error.
  * `fastpath function call`: Executing a fast-path function.
  * `disabled`: track_activities has been disabled for this backend.
* `wait_event & wait_event_type`: The event type that the backend is waiting for. This can be an extensive list. Watch for locks on database objects here.
* `query`: The text of the backends most recent, or currently active, query. As per the documentation, this is truncated at 1024 bytes, changeable with “track_activity_query_size” in the server configuration.
* `backend_type`: There’s a few different types, but for most connections from external clients, this will be “client backend”.

**Find queries running longer than 5 minutes**

```
SELECT
  pid,
  user,
  pg_stat_activity.query_start,
  now() - pg_stat_activity.query_start AS query_time,
  query,
  state,
  wait_event_type,
  wait_event
FROM pg_stat_activity
WHERE (now() - pg_stat_activity.query_start) > interval '5 minutes';
```

**Kill long-running PostgreSQL query processes**
Where some queries look like they’re not going to finish, you can use the pid (process ID) from the pg_stat_activity 
or pg_locks views to terminate the running process.

* `pg_cancel_backend(pid)` will attempt to gracefully kill a running query process.
* `pg_terminate_backend(pid)` will immediately kill the running query process, but potentially have side affects across additional queries running on your database server. The full connection may be reset when running pg_terminate_backend, so other running queries can be affected. Use as a last resort.

**Finding blocked processes and blocking queries**
The pg_blocking_pids() function is a useful shortcut to find the database connections / sessions that are blocking another session. The pg_blocking_pids() function returns an postgreSQL array of PIDs that are blocking the specified server process PID that you provide to the query. 
Typically, a server process blocks another if it holds a lock that is needed by the second process.

```
SELECT
    activity.pid,
    activity.usename,
    activity.query,
    blocking.pid AS blocking_id,
    blocking.query AS blocking_query
FROM pg_stat_activity AS activity
JOIN pg_stat_activity AS blocking ON blocking.pid = ANY(pg_blocking_pids(activity.pid));
```

**Viewing locks with table names and queries**

```
select 
    relname as relation_name, 
    query, 
    pg_locks.* 
from pg_locks
join pg_class on pg_locks.relation = pg_class.oid
join pg_stat_activity on pg_locks.pid = pg_stat_activity.pid
```

### Partitioned tables
When partitioned tables were first introduced in PostgreSQL 10, they didn’t support foreign keys at all.
Now that PostgreSQL 12 is out, we consider foreign keys to be fully compatible with partitioned tables.

For example, we have table. And we want to divide this table into partitions.
```
CREATE TABLE items (
    item_id integer PRIMARY KEY,
    description text NOT NULL
)
```

We need a filed for dividing.
```
CREATE TABLE items (
    item_id integer PRIMARY KEY,
    description text NOT NULL
)
```
Now we can split the table into partitions based on the day number in the month.
```
-- Release table name
-- Move table to partiion number one
alter table items rename to items_1;

-- Recreate main table
create table items (like items_1) partition by list (date_part('day'::text, create_date));

-- Attach old table to new. Partition key is the day number in the month.
alter table items attach partition items_1 for values in ('1');

-- Create table for all days in month from 2 to 31

create table items_2 (like items_1 including all);
alter table items attach partition items_2 for values in ('2');

create table items_3 (like items_1 including all);
alter table items attach partition items_3 for values in ('3');

create table items_4 (like items_1 including all);
alter table items attach partition items_4 for values in ('4');

create table items_5 (like items_1 including all);
alter table items attach partition items_5 for values in ('5');

create table items_6 (like items_1 including all);
alter table items attach partition items_6 for values in ('6');

create table items_7 (like items_1 including all);
alter table items attach partition items_7 for values in ('7');

create table items_8 (like items_1 including all);
alter table items attach partition items_8 for values in ('8');

create table items_9 (like items_1 including all);
alter table items attach partition items_9 for values in ('9');

create table items_10 (like items_1 including all);
alter table items attach partition items_10 for values in ('10');

create table items_11 (like items_1 including all);
alter table items attach partition items_11 for values in ('11');

create table items_12 (like items_1 including all);
alter table items attach partition items_12 for values in ('12');

create table items_13 (like items_1 including all);
alter table items attach partition items_13 for values in ('13');

create table items_14 (like items_1 including all);
alter table items attach partition items_14 for values in ('14');

create table items_15 (like items_1 including all);
alter table items attach partition items_15 for values in ('15');

create table items_16 (like items_1 including all);
alter table items attach partition items_16 for values in ('16');

create table items_17 (like items_1 including all);
alter table items attach partition items_17 for values in ('17');

create table items_18 (like items_1 including all);
alter table items attach partition items_18 for values in ('18');

create table items_19 (like items_1 including all);
alter table items attach partition items_19 for values in ('19');

create table items_20 (like items_1 including all);
alter table items attach partition items_20 for values in ('20');

create table items_21 (like items_1 including all);
alter table items attach partition items_21 for values in ('21');

create table items_22 (like items_1 including all);
alter table items attach partition items_22 for values in ('22');

create table items_23 (like items_1 including all);
alter table items attach partition items_23 for values in ('23');

create table items_24 (like items_1 including all);
alter table items attach partition items_24 for values in ('24');

create table items_25 (like items_1 including all);
alter table items attach partition items_25 for values in ('25');

create table items_26 (like items_1 including all);
alter table items attach partition items_26 for values in ('26');

create table items_27 (like items_1 including all);
alter table items attach partition items_27 for values in ('27');

create table items_28 (like items_1 including all);
alter table items attach partition items_28 for values in ('28');

create table items_29 (like items_1 including all);
alter table items attach partition items_29 for values in ('29');

create table items_30 (like items_1 including all);
alter table items attach partition items_30 for values in ('30');

create table items_31 (like items_1 including all);
alter table items attach partition items_31 for values in ('31');
```

Example partition by range
```
create table test_202310_1 partition of test for values from ('2023-08-11') to ('2023-08-01');
```
## List indexes in Postgres
```
SELECT
tablename as "TableName",
indexname as "Index Name",
indexdef as "Index script"
FROM
pg_indexes
WHERE
schemaname = 'public'
ORDER BY
tablename,
indexname;
```

## Using indexes
There are various type of indexes. Postgres supports the following index types.

* [B-Tree](#b-tree)
* [Hash](#hash-index)
* [GiST](#gist-indexes)
* [SP-GiST](#sp-gist-indexes)
* [GIN](#gist-indexes)
* [BRIN](#brin-index)

### B-Tree
B-tree is the default index in Postgres and is best used for specific value searches, scanning ranges, 
data sorting or pattern matching. If we don’t specify any particular index type in the CREATE INDEX command, 
Postgres creates a B-tree index.
Postgres automatically creates a B-tree index if we define a primary or unique key on a table. 
You can also define the index on multiple columns (composite key) as well
```
CREATE INDEX IX_Addresses_id on addresses(id);
```

### Hash index
Hash indexes are best suited to work with equality operators. The equality operator looks for the exact match of data. 
Starting from Postgres 9.x version, the hash indexes are WAL-logged and crash-safe.
```
CREATE INDEX IX_Addresses_city on addresses using HASH("city");
```

### GiST indexes
The Generalized Search Tree (GiST) is balanced, and it implements indexing schemes for new data types in a familiar balanced tree structure. 
It can index complex data such as geometric data and network address data.
```
create index on demodata using gist(p);
```
### SP-GiST indexes
The SP-GiST index refers to a space partitioned GiST index. 
It is useful for indexing non-balanced data structures using the partitioned search tree.
```
Create index on demodata using spgist(p);
```
### GIN indexes
The Generalized Inverted Index (GIN) is beneficial for indexing columns that have composite types. 
It is best suited for data types such as **JSONB**, **Array**, **Range types** and **full-text search**.

Suppose users want to get a record count of all whose first name is like %raj%, and the last name is like ‘%aec%.
```
CREATE EXTENSION IF NOT EXISTS pg_trgm;
CREATE INDEX idx_id_gin ON users_user USING gin (upper(id::text) gin_trgm_ops);
CREATE INDEX idx_first_name_gin ON users_user USING gin (upper(first_name::text) gin_trgm_ops);
CREATE INDEX idx_last_name_gin ON users_user USING gin (upper(last_name::text) gin_trgm_ops);
CREATE INDEX idx_phone_number_gin ON users_user USING gin (upper(phone_number::text) gin_trgm_ops);
CREATE INDEX idx_email_gin ON users_user USING gin (upper(email::text) gin_trgm_ops);
```

### BRIN index
The BRIN index is also known as Block Range Index. It stores the summary of blocks (minimum value, maximum value and page number). 
Once a BRIN index is implemented, it uses the BRIN values for validating each page. 
In case the page is not modified, its BRIN value remains the same.

It is useful for extensive data such as timestamps and temperature sensor data. It also uses less storage compared to a B-tree index.

## Why Is My Query Not Using an Index?
There are many reasons why the Postgres planner can choose to not use an index. 
Most of the time, the planner chooses correctly, even if it isn’t obvious why. 
It’s okay if the same query uses an index scan on some occasions but not others. 
The number of rows retrieved from the table can vary based on the particular constant values the query retrieves. 
So, for example, it’s correct for the query planner to use an index for the query select * from foo where bar = 1, 
and yet not use one for the query select * from foo where bar = 2 
if there happened to be far more rows with “bar” values of 2. 
When this happens, a sequential scan is most likely much faster than an index scan, 
so the query planner has in fact correctly judged that the cost of performing the query that way is lower.

## Partial Indexes
A partial index covers just a subset of a table’s data. It’s an index with a WHERE clause. 
The idea is to increase the efficiency of the index by reducing its size. 
A smaller index takes less storage, is easier to maintain, and is faster to scan.

```
CREATE INDEX articles_flagged_created_at_index ON articles(created_at) WHERE flagged
```

## Expression Indexes
Expression indexes are useful for queries that match on some function or modification of your data. 
Postgres allows you to index the result of that function so that searches become as efficient as searching by raw data values.
```
CREATE INDEX users_lower_email ON users(lower(email));
```

## Unique Indexes
A unique index guarantees that the table doesn’t have more than one row with the same value. 
It’s advantageous to create unique indexes for two reasons:data integrity and performance. 
Lookups on a unique index are very fast. Even partial unique indexes on expressions are possible.

## Multi-Column Indexes
While Postgres can create multi-column indexes, it’s important to understand when it makes sense to do so. 
The Postgres query planner can combine and use multiple single-column indexes in a multi-column query by performing a bitmap index scan. 
In general, you can create an index on every column that covers query conditions and in most cases, 
Postgres will use it. So, make sure to benchmark and justify the creation of a multi-column index before you create one.

However, there are cases where a multi-column index clearly makes sense. An index on columns `(a, b)` can be used 
by queries containing `WHERE a = x AND b = y`, or queries using `WHERE a = x` only, 
but aren’t used by a query using `WHERE b = y`. So if this matches the query patterns of your application, 
the multi-column index approach is worth considering. 
Also, note that in this case creating an index on a alone would be redundant.

## B-trees and Sorting
B-Tree index entries are sorted in ascending order by default. 
In some cases, it makes sense to supply a different sort order for an index. 
Take the case when you’re showing a paginated list of articles, sorted by most recent published first. 
We can have a `published_at` column on our articles table. For unpublished articles, the published_at value is `NULL`.
```
CREATE INDEX articles_published_at_index ON articles(published_at DESC NULLS LAST);
```

## Managing and Maintaining Indexes
Postgres decides to perform a sequential scan on any query that hits a significant portion of a table. 
If you do have an index on that column, it’s a dead index that’s never used - and indexes aren’t free: 
they come at a cost in terms of storage and maintenance.

When tuning a query and understanding what indexes make the most sense, 
be sure to use a database as similar as possible to what exists, or will exist in production. 
Whether an index is used or not depends on a number of factors, including the Postgres server configuration, 
the data in the table, the index, and the query.

When you’re ready to apply an index on your production database, keep in mind that creating an index locks the table 
against writes. For large tables that can mean your site is down for hours. 
Fortunately, Postgres allows you to `CREATE INDEX CONCURRENTLY`, which takes much longer to build but doesn’t 
require a lock that blocks writes. Ordinary `CREATE INDEX` commands require a lock that blocks writes but not reads.

Finally, indexes will become fragmented and unoptimized after some time, especially if the rows in the table are often 
updated or deleted. In those cases, it can be required to perform a `REINDEX` leaving you with a balanced and optimized 
index. However, be cautious about reindexing large indexes as write locks are obtained on the parent table. 
One strategy to achieve the same result on a live site is to build an index concurrently on the same table and columns 
but with a different name, and then drop the original index and rename the new one.