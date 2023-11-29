# Postgres

* [Create database and user](#create-database-and-user)
* [Analyze queries](#analyze-queries)
* [Find slow, long-running, and Blocked Queries](#find-slow-long-running-and-blocked-queries)
* [Partitioned tables](#partitioned-tables)

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

