# Postgres

* [Create database and user](#create-database-and-user)
* [Find slow, long-running, and Blocked Queries](#find-slow-long-running-and-blocked-queries)

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