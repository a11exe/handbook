# SQL

* [Data Type Formatting](#data-type-formatting)
* [Copy a Table](#copy-a-table)
* [Insert with select](#insert-with-select)
* [Insert in loop](#insert-in-loop)
* [Random from set](#random-from-set)
* [Stored procedure](#stored-procedure)
* [Transactions](#transactions)
* [Block structure](#block-structure)
* [IF Statement](#if-statement)
* [For Loops](#for-loops)
* [With clause](#with-clause)
* [Complex example](#complex-example)
* [Generate dates](#generate-dates)
* [Start and end of day](#start-and-end-of-day)
* [Test jsonb query](#test-jsonb-query)
* [Temp table](#temp-table)

## Data Type Formatting
```
SELECT coalesce(type, 'null) || coalesce(to_char(oper_date, 'YYYY-mm-dd HH:MM:SS'), 'null') || md5(hash)::uuid || sum::text) from operations;
```

## Copy a Table
Creating new tables that would either have the same data or data of the same table with certain operations performed on them.

We will discuss the following 3 cases:

* Copy Table with the same structure and data.
* Copy Table with the same structure and no data.
* Copy Table with the same structure and partial data.

```
-- create a new table named copy_students with the same structure and data as the students table
CREATE TABLE copy_students AS TABLE students;  
-- no data
CREATE TABLE new_table AS TABLE old_table WITH NO DATA;
-- copy by condition
CREATE TABLE new_table AS TABLE old_table WHERE condition;
```

Copy the structure of a table into a new table, without data, including all keys and constraints

```
CREATE TABLE new_table_name (LIKE old_table_name INCLUDING ALL);
```

## Transactions
The `BEGIN` keyword is used to start a transaction block.
The `COMMIT` keyword saves changes to the database.
Please note: The `COMMIT` and `END` keywords are same.

```
BEGIN;

 INSERT INTO BankStatements (
     customer_id,
    full_name,
        balance

)
    VALUES(
       4, 'Priya chetri', 500
        )
;
        
COMMIT;
```

## Insert with select
```
insert into lesson (id, "name", description, link, created_at, time_seconds, last_updated_at, enabled)
	select 	nextval(pg_get_serial_sequence('lesson', 'id')) as id,
			l."name",
			l.description,
			l.link,
			now(),
			l.time_seconds,
			now(),
			true
	from lesson l
	where l.id = 1;
```

## Insert in loop
```sql
do $$
begin
for r in 1..1000 loop
insert into schema_name.table_name(id) values(r);
end loop;
end;
$$;
```

## Random from set
```sql
select ('{Foo,Bar,Poo}'::text[])[ceil(random()*3)];
```

## Stored procedure
```
create or replace procedure transfer(
   sender int,
   receiver int, 
   amount dec
)
language plpgsql    
as $$
begin
    -- subtracting the amount from the sender's account 
    update accounts 
    set balance = balance - amount 
    where id = sender;

    -- adding the amount to the receiver's account
    update accounts 
    set balance = balance + amount 
    where id = receiver;

    commit;
end;$$
```
Calling a stored procedure
```
call stored_procedure_name(argument_list);
```

## Block structure
The following example illustrates a very simple block. It is called an anonymous block.
```
do $$ 

declare
  film_count integer := 0;
begin
   -- get the number of films
   select count(*) 
   into film_count
   from film;
   -- display a message
   raise notice 'The number of films is %', film_count;
end first_block $$;
```

## IF Statement
```
DO $$
DECLARE
   a integer := 10;
   b integer := 10;
BEGIN 
  IF a > b THEN 
     RAISE NOTICE 'a is greater than b';
  ELSIF a < b THEN
     RAISE NOTICE 'a is less than b';
  ELSE
     RAISE NOTICE 'a is equal to b';
  END IF;
END $$;
```

## For Loops
PostgreSQL provides the for loop statements to iterate over a range of integers or over a result set or over the result set of a dynamic query.

```
do $$
begin
   for cnt in 1..10 loop
    raise notice 'cnt: %', cnt;
   end loop;
end; $$
```

```
do $$
begin
   for cnt in reverse 10..1 loop
      raise notice 'cnt: %', cnt;
   end loop;
end; $$
```

```
do
$$
declare
    f record;
begin
    for f in select employee_id, full_name 
           from employees 
           order by employee_id desc, full_name
           limit 10 
    loop 
    raise notice '% - % ', f.employee_id, f.full_name;
    end loop;
end;
$$;
```



## With clause
It helps in breaking down complicated and large queries into simpler forms.
These statements are often referred to as common table expressions or CTEs. 
The CTEs are like temporary tables that exist only during the execution of the query.

```
With CTE AS
(Select
 ID
, NAME
, AGE
, ADDRESS
, SALARY
FROM COMPANY )
Select * From CTE;
```

The query in the example effectively moves rows from COMPANY to COMPANY1. 
The DELETE in WITH deletes the specified rows from COMPANY, returning their contents by means of its RETURNING clause; 
and then the primary query reads that output and inserts it into COMPANY1

```
CREATE TABLE COMPANY1(
   ID INT PRIMARY KEY     NOT NULL,
   NAME           TEXT    NOT NULL,
   AGE            INT     NOT NULL,
   ADDRESS        CHAR(50),
   SALARY         REAL
);

WITH moved_rows AS (
   DELETE FROM COMPANY
   WHERE
      SALARY >= 30000
   RETURNING *
)
INSERT INTO COMPANY1 (SELECT * FROM moved_rows);
```

A recursive query is a query that refers to a recursive CTE. The recursive queries are useful in many situations such as querying hierarchical data like organizational structure, bill of materials, etc.

The following illustrates the syntax of a recursive CTE:
```
WITH RECURSIVE cte_name AS(
        CTE_query_definition -- non-recursive term
        UNION [ALL]
        CTE_query definion  -- recursive term
) SELECT * FROM cte_name;
```

A recursive CTE has three elements:

* Non-recursive term: the non-recursive term is a CTE query definition that forms the base result set of the CTE structure.
* Recursive term: the recursive term is one or more CTE query definitions joined with the non-recursive term using the UNION or UNION ALL operator. The recursive term references the CTE name itself.
* Termination check: the recursion stops when no rows are returned from the previous iteration.

PostgreSQL executes a recursive CTE in the following sequence:

1. Execute the non-recursive term to create the base result set (R0).
2. Execute recursive term with Ri as an input to return the result set Ri+1 as the output.
3. Repeat step 2 until an empty set is returned. (termination check)
4. Return the final result set that is a UNION or UNION ALL of the result set R0, R1, … Rn

We will create a new table to demonstrate the PostgreSQL recursive query.

```
CREATE TABLE employees (
	employee_id serial PRIMARY KEY,
	full_name VARCHAR NOT NULL,
	manager_id INT
);
```

Fill data

```
INSERT INTO employees (
	employee_id,
	full_name,
	manager_id
)
VALUES
	(1, 'Michael North', NULL),
	(2, 'Megan Berry', 1),
	(3, 'Sarah Berry', 1),
	(4, 'Zoe Black', 1),
	(5, 'Tim James', 1),
	(6, 'Bella Tucker', 2),
	(7, 'Ryan Metcalfe', 2),
	(8, 'Max Mills', 2),
	(9, 'Benjamin Glover', 2),
	(10, 'Carolyn Henderson', 3),
	(11, 'Nicola Kelly', 3),
	(12, 'Alexandra Climo', 3),
	(13, 'Dominic King', 3),
	(14, 'Leonard Gray', 4),
	(15, 'Eric Rampling', 4),
	(16, 'Piers Paige', 7),
	(17, 'Ryan Henderson', 7),
	(18, 'Frank Tucker', 8),
	(19, 'Nathan Ferguson', 8),
	(20, 'Kevin Rampling', 8);
```

The following query returns all subordinates of the manager with the id 2.

```
WITH RECURSIVE subordinates AS (
	SELECT
		employee_id,
		manager_id,
		full_name
	FROM
		employees
	WHERE
		employee_id = 2
	UNION
		SELECT
			e.employee_id,
			e.manager_id,
			e.full_name
		FROM
			employees e
		INNER JOIN subordinates s ON s.employee_id = e.manager_id
) SELECT
	*
FROM
	subordinates;
```

How it works:

The recursive CTE, subordinates, defines one non-recursive term and one recursive term.
The non-recursive term returns the base result set R0 that is the employee with the id 2.

```
 employee_id | manager_id |  full_name
-------------+------------+-------------
           2 |          1 | Megan Berry
```

The recursive term returns the direct subordinate(s) of the employee id 2. This is the result of joining between the employees table and the subordinates CTE. The first iteration of the recursive term returns the following result set:

```
 employee_id | manager_id |    full_name
-------------+------------+-----------------
           6 |          2 | Bella Tucker
           7 |          2 | Ryan Metcalfe
           8 |          2 | Max Mills
           9 |          2 | Benjamin Glover
```

PostgreSQL executes the recursive term repeatedly. The second iteration of the recursive member uses the result set above step as the input value, and returns this result set:

```
 employee_id | manager_id |    full_name
-------------+------------+-----------------
          16 |          7 | Piers Paige
          17 |          7 | Ryan Henderson
          18 |          8 | Frank Tucker
          19 |          8 | Nathan Ferguson
          20 |          8 | Kevin Rampling
```

The third iteration returns an empty result set because there is no employee reporting to the employee with the id 16, 17, 18, 19, and 20.

PostgreSQL returns the final result set that is the union of all result sets in the first and second iterations generated by the non-recursive and recursive terms.

```
 employee_id | manager_id |    full_name
-------------+------------+-----------------
           2 |          1 | Megan Berry
           6 |          2 | Bella Tucker
           7 |          2 | Ryan Metcalfe
           8 |          2 | Max Mills
           9 |          2 | Benjamin Glover
          16 |          7 | Piers Paige
          17 |          7 | Ryan Henderson
          18 |          8 | Frank Tucker
          19 |          8 | Nathan Ferguson
          20 |          8 | Kevin Rampling
(10 rows)
```

Factorial

```
WITH RECURSIVE r AS (
    SELECT 
        1 AS i, 
        1 AS factorial
    
    UNION 
    
    SELECT 
        i+1 AS i, 
        factorial * (i+1) as factorial 
    FROM r
    WHERE i < 10
)
SELECT * FROM r;
```

Result:

```
 i  | factorial 
----+-----------
  1 |         1
  2 |         2
  3 |         6
  4 |        24
  5 |       120
  6 |       720
  7 |      5040
  8 |     40320
  9 |    362880
 10 |   3628800
(10 rows)
```

## Complex example

```
do
$$
declare
    f record;
   	user_id uuid;
begin
	select id into user_id from users where login = 'a11exe' limit 1;
    for f in select * 
           from news 
		   where title like 'news%' 
    loop
	with news_data as (select id as news_id from news where id = f.id)
    insert
    into news_user_saved
    (news_id, user_id)
    select news_id,
    		user_id
    from news_data;
    end loop;
end;
$$;
```

## Generate dates
```
SELECT date_trunc('day', dd):: date
FROM generate_series
        ( '2007-02-01'::timestamp 
        , '2008-04-01'::timestamp
        , '1 day'::interval) dd;
```

## Start and end of day
```
select date_trunc('day', val) as start_day,
       (date_trunc('day', val) + interval '1 day' - interval '1 second') as end_day
```

## Test jsonb query
```sql
do
$$
declare
  _iterations INT = 1000;
  _uuid uuid;
  _id TEXT;
  _uri text;
  _channel text;
  _system text;
  _start TIMESTAMP;
  _end TIMESTAMP;
begin
  _start := clock_timestamp();
  select id
    into _uuid
    from message_log where uri = 'CreateMessage'
    limit 1;
  for i in 1.._iterations loop
    select id, uri, content #>> '{Request, Channel}', system
    into _id, _uri, _channel, _system
    from message_log where id = _uuid;
--    raise notice 'Data: % % %', _id, _uri, _system;
     end loop;
   _end := clock_timestamp();
   raise notice 'Execution time of % iterations in ms = %', _iterations, 1000 * (extract(epoch from _end - _start));
end;
$$;
```

## Temp table
CTE doesn't work with multiple queries. If you need a temp table for more than one query you can do:
```
begin;

create temp table temp_table as (
  select id
  from table1
);

delete from table2
where id in (select id from temp_table);

insert into table2 (id)
select id from temp_table;

drop temp_table;

commit;
```
Using create temp table with CTE:
```
create temp table foo
as
with cte1 as (
  ...
), cte2 as (
 ...
), cte3 as (
 ...
), cte4 as (
 ...
), cte5 as (
 ...
) 
select *
from cte5;
```