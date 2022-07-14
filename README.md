- [SQL logging enable in Django](#sql-logging-enable-in-django)
- [BTREE index](#btree-index)
- [Some basic help](#some-basic-help)
- [Creating a database](#creating-a-database)
- [List of datatatypes in postgresql](#list-of-datatatypes-in-postgresql)
- [Drop a database](#drop-a-database)
- [Connect to a database](#connect-to-a-database)
- [Create a table](#create-a-table)
- [General options](#general-options)
- [Definition](#definition)
- [Options](#options)
- [Arguments](#arguments)
- [Parameters](#parameters)
- [Aggregate function](#aggregate-function)
- [MIN](#min)
- [MAX](#max)
- [AVERAGE](#average)
- [SUM](#sum)
- [COUNT](#count)

## SQL logging enable in Django 
- This will log the the sql executed in debug mode
```
LOGGING = {
    'loggers':  {
        'django.db.backends': {'level': 'DEBUG',}
    }
}
```

## BTREE index
- King of all index

## Some basic help
```
\? # For help in psql
\l # List all the database
\q # To quit the database

```

## Creating a database
- Normal syntax `create table table_name (
    column name + data type + constraints if any
    )` 
```
create database person (
    id int, 
    first_name varchar(50),
    last_name varchar(50),
    gender varchar(6)
    date_of_birth TIMESTAMP
)
```
## List of datatatypes in postgresql
- `https://www.postgresql.org/docs/current/datatype.html`
  
## Drop a database
- Drop command is very dangerous
- All of the contents will be lost in matter of millisecond.

```
drop database account
```

## Connect to a database
- Here `account` is the name of database.
```
psql -h localhost --username postgres --password postgres -p 5433 account
```

## Create a table 
```
create table person(
    id bigserial not null primary key, 
    first_name varchar(50) not null,
    last_name varchar(50) not null,
    gender varchar(7) not null,
    date_of_birth Date not null,
)
```
- Listing of table 
- ```
  \d # describe the table
  \l # list the tables
  \c account # connect to account database.
  ```

## Insert data 
```
insert into person (
    first_name, 
    last_name, 
    gender, 
    date_of_birth
) values(
    'manoj',
    'gautam',
    'male'
    '1990-05-02'
)
```

## Run sql file 
```
psql> \i /home/nix1947/person.sql
```

## Alter table
```
alter table person add column date_of_birth varchar(50) not null
```

## Schema
- Logical container of a tables and other objects inside a database. 
- You can have multiple schemas in a database
- `SCHEMA = Tables + otherdb objects(functions, dictionaries, parsers, templates configurations, collations, domains, sequences, trigger, types, views)`
- public schemas. everyone has access to it. 
- You can create schema  group related to different  objects and assign a permission to the objects on schema.
- You can crete an Schema for `IT Dept` `finance Dept` and for others with specific permissions.
- We can also lock/limit the access for certain tables within an schema. 
- 

## Table Space
  - Tablespace is a location where data is stored. 
  - By default postgres has two default tablespace which are `pg_default` and `pg_global` 
  - `pg-default`
    - Used for storing user data
  - `pg_global`
    - Used for storing system data. 

## Functions in postgresql
- Function are block of reusable sql code. 
- Function can return scalar value or a  list of result.
- Also can return composite objects. 
- Function has `definition, options, arguments, parameters, security` 
- Function overloading is supported in postgresql.

**Syntax to create a function**
```
CREATE OR REPLACE FUNCTION totalRecords() 
    RETURNS integer AS $total 
    DECLARE 
     -- Declare some variables here
    BEGIN
        -- Your logic goes here RETURN 
    END
``` 

- Create an `log_record()` function to log every transaction to store the history of records. 

**Function GUI example** 
```
General options
----------------
Name: name of function 
Owner: owner of a unction 
schema: public
comment: Description of a function

Definition
----------
Return type: integer
Language: plpgsql
Code:  BEGIN 
            RETURN i + val;
        END;

Options
-------
Volatility: volatile/stable/immutable -- choose volatile as function return value are different and changed. 

Returns a set: Yes/no 
strict: yes/no: when any of the passed argument are null in case of Yes, function will not be executed. 
Security of definer: No/Yes -- Means function will be executed with privileges of the user.
window: Yes/No -- Function is a window function rather than a plain function
parallel: UNSAFE/SAFE/RESTRICTED -- SAFE: operation indicate the function is not conflicted with the execution query.
estimated cost: 
estimated row: used to specify the positive number. 
Leak proof: yes/no -- Indicate whether the function has side proof or not. 

Arguments
-------
Data type: integer 
Mode: IN 
Argument Name: i 
default value: 

Data type: integer
Mode: IN
argument Name: val 
default value: 

Parameters
---------
SQL 
```
the above will generate the query given below. 
```
CREATE FUNCTION public.inc(IN i integer, IN val integer)

RETURNS integer 
LANGUAGE 'plpsql'
VOLATILE
AS $function$
BEGIN 

    RETURN i + val;
END;
$functions$; 

ALTER FUNCTION public.inc(integer, integer) owner to postgres;

COMMENT ON Function public.inc(integer, integer)
IS 'increase an integer by a value';
```
**calling a new function**
```
SELECT inc(20, 30)
```

## Using cast and operators
- Use to convert one data type into another data type
- Casts used with functions to perform conversion
- You can create custom casts to override the default. 
- Operator is a symbolic operation in database. 
- Casting operators are fall under the casts group. 


## Sequences
- Sequences are used to manage auto increment columns 
- This is usually identified with ID column which is unique. 

``` 
CREATE SEQUENCE public.mysequence 
    INCREMENT 1
    START 200
    MINVALUE 1 
    MAXVALUE 200
    CACHE 1

```

## Creating extension
- used to wrap other objects into a single unit so it is easier to maintain. 
- Extension includes `casts, indexes, functions and other objects`
  

## Creating stored procedure. 
- Set of sql statement that can be given a name and stored on RDBMS and group when required.
- Can also shared by multiple application or programs.
- Allow us to extend the functionality of database with user defined functions. 
- SPC can be used to define various types of function such as `triggers` or custom aggregated functions. 
- Advantages.    
  - Reduce network traffic by reducing RTT between application and database server.
  - Increase application performance. 
  - This are reusable in any application. 
- Disadvantages
  - Slow software development due to it requires specialized skill as many developer lack skills. 
  - It makes a difficult to manage version and hard to debug.
  - It may not be portable to other DMBS like MySQL or MsSQL. 


## SQL UNION operator.
- used to combine results sets of multiple queries into a single result.
- Removes all duplicate rows.
- Both queries mut return same number of columns. 
- The corresponding columns in the queries must have compatible data types.

```
select col1, col2 from t
union 
select c1, c2 from t2

```
**Example on DVD rental**
```
select customer_id from customer 
union 
select customer_id from payment
```

## Union All operator. 
- Does not remove any duplicates from the both queries result.
- It keeps the duplicate. 
- Must have same number of columns.
- They should have same compatible data types. 
  
**SYNTAX**
```
select customer_id
from customer 
union
select customer_id
from payment;
```

## Intersect operator
- Used to combine result sets of two or more select statements into a single result.
- The intersect operator returns all rows in both result sets.
- The number of columns that appear in the select statement must be the same.
- the data types of the columns must be compatible.

```
select c1, c2, from t1
intersect
select c1, c2 from t2
```

## Except Operator
- Return rows based on comparison between the result sets of two or more queries. 
- Return the result from first query that are absent in the second query.
- Also the number of columns and order must be the same in the participating query.
  
```
select film_id, title from file 
except
 select distinct flim_id, title 
 from inventory 
 ```

 ## In operator.
 - specify or check against multiple values in a `WHERE` clause.

```
SELECT name from tbl_name
where column_name in (v1, v2)

select name from t1 where name in(select name in table2)
```
 
 ## And operator
 - Returns records matching all specified conditions.
 - Included in the where clause.
 - filter records based on more than one conditions. 

```
select * from city where city_id597 and ity='zaria'; 
```

## Or operator
- Return records matching any specified condition.
- Included in the where clause.
- Filters records based on more than one condition. 

```
select c1,c2 where tbl where condition1 or condition2
```

## Using And and OR 
```
select * from city where 
(country_id=91 and city='zaria') 
or
(city_id=589)
```

## Like operator
- Used to query data by using a matching techniques.
- Used with wild cards in the WHERE clause to search and match specified patterns.
- % wildcard allows you to match string of any length characters including zero. 
- `_` wild card allows you to match a single character

```
select c1, c2 from table_name 
where column like pattern;
```

```
select first_name from customer where first_name like '_her%'
```
## Table joins

### Left join
- Natural Join / Inner join. 
-  Used to retrieve data from multiples tables using INNER JOIN clause.
-   Return records that have matching values from columns in both tables. 
```

select customer.customer_id, first_name, last_name, email, amount, payment_date from customer inner join payment on payment.customer_id=customer.customer_id;
```

### Left join 
- Used to retrieve data from multiple tables using left join clause.
- Return ALL data from left table and any matched data from right table if no matching found in right no data is retrieve from right table but all data from left table are return.

```
select film.film_id,
film.title,
inventory_id
from 
film
LEFT JOIN inventor ON inventory.film_id=film.film_id;
``` 

### Full outer join
- Used to retrieve data from multiple tables using FULL OUTER JOIN clause.
- returns records from the left table and right table. 
- if rows in joined table do not match the full outer joins sets NULL values. 
- For the matching rows a single row is included in the result from both table. 

```
select film.film_id, 
film.title,
inventory_id
from film
full outer join inventory on film.film_id = inventory.film_id
where inventory.film_id is null
order by film_id asc;
```

### Cross join
- Also called cartesian join. 
- Used to retrieve data from multiples tables using cross join clause.
- Used without specify the matching columns. 
- Combine two tables together completely.
- Doest not require the tables to have matching columns in the  JOIN clause.
- produce the cartesian product of rows from combined joined tables. 

```
select * from table1 cross join table2
```
```
select * from actor cross join city
```

### Natural join 
- Used to retrieve data from multiple table using natural join clause.
- Creates an implicit join based on matching column names in the joined tables.
- Natural join can be an INNER JOIN, LEFT JOIN, RIGHT JOIN.
- Postgresql will use the inner join by default.
- It don't require you to specify the columns as it work the common column, so do prefer using inner join on clause as natural join can give you an unexpected results sometimes. 

```
select * from customer 
natural join 
payment 
```

```
select * from customer 
inner join payment using(customer_id)
```

```
select * from customer left join payment 
using(customer_id)
```

```
select * from customer right join payment
using(customer_id)
```

## Aggregate function  

## MIN 

## MAX

## AVERAGE

## SUM 

## COUNT

