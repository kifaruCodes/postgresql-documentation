# **postgresql-documentation**

# Overview
Personal documentation for postgreSQL

## Table of content
- [Installation](#installation)
- [pSQL commands](#psql-commands)
- [SQL commands](#sql-commands)
- - [create database](#create-database)
- - [create table](#create-table)
- - [create temporary table](#create-temporary-table)
- - [Copy a table](#copy-a-table)
- - [drop a table](#drop-table)
- - [Truncate table](#truncate-table)
- - [Alter table](#alter-table)
- - [Insert Data](#alter-table)
- - [Upsert Data](#upsert-data)
- - [Update Data](#update-data)
- - [Delete Data](#delete-data)
- - [Select Statement](#select-statement)
- - - [Select Where](#select-where)
- - - [Select Group By](#select-group-by)

## **Installation:**
([back to table of content](#table-of-content))

Linux:
```bash
$ sudo apt install postgresql
```

Windows :
- download from : https://www.postgresql.org/download/windows/

## **PSQL Commands**
([back to table of content](#table-of-content))
- `\q` : exit
- `\l` : view list of all databases (show databases)
- `\c database` : Connect to database (use db)
- `\du` : database users ie list all roles
- `\d` `\dt` : show tables
- `\d table` : describe table (desc table)
- `\dt table` : show table details

## **SQL commands**
([back to table of content](#table-of-content))

## Create database
---
([back to table of content](#table-of-content))

granting privileges to db user

 ```sql
GRANT ALL PRIVILEGES ON DATABASE "database" TO user;
```

Creating a database
```
CREATE DATABASE database;
```

Deleting a database
```sql
DROP DATABASE database;
```

A database will not be dropped/deleted if the database has active sessions connected to it.

To drop a database with active sessions, you can revoke access to the database with the below command:

```sql
REVOKE CONNECTION ON DATABASE database_name    FROM PUBLIC;
```

view database connection information
Create a table (eg person)
```
/conninfo 
```

## Create table
---
([back to table of content](#table-of-content))

Create table in database
```sql
CREATE TABLE person(
    id int,
    fname VARCHAR(100),
    lname VARCHAR(100),
    date_of_birth TIMESTAMP,
)
```
another example:
```sql
create table if not exists person(
    id int primary key,
    fname varchar(100) not null,
    lname varchar(100) not null,
    birthdate date,
    email varchar(50) unique
);
```

Create table from another table:
```sql
 create table employee
 as
 select * from person
```

The above will create a table `employee` with the same attributes as `person`. It will create the same column names and data types as the `person` table

You can create a table from another table with specific tables as below:

``` sql
create table employee2
as 
select id, fname from person;
```

to specity differant names, put the query as below:
```sql
create table employee3 (employee_id, employee_fname)
as
select id, fname from employee2
```

to avoid errors during creation, you can add the `if not exists` to the query
```sql
create table if not exists employee3 (employee_id, employee_fname)
as
select id, fname from employee2
```

## Create temporary table
---
([back to table of content](#table-of-content))

A temporary table is a table that lasts only in the duration of the session.
One you log out of the db session, it is deleted.

When you change databases, the temporary table is also deleted.

```sql
create temporary table temp_people (
    id int,
    fname varchar(10),
    lname varchar(10),
    birthdate date
)
```

## Copy a table
---
([back to table of content](#table-of-content))

```sql
--- create/copy new table
CREATE TABLE new_table_name 
AS 
TABLE existing_table;

--- create copy of table without the data
CREATE TABLE new_table_name 
AS
TABLE existing_table
[WITH NO DATA];

-- create new table with conditions
CREATE TABLE new_table_name 
AS
SELECT * FROM existing_table
WHERE conditions;
```

example

```sql
CREATE TABLE EMPLOYEE 
AS
TABLE PERSON;

CREATE TABLE PERSON_BKP 
AS
TABLE PERSON
WITH NO DATA;
```

## Drop table
---
([back to table of content](#table-of-content))

The below script deletes a database.
```sql
DROP TABLE IF EXISTS employee;
```

A database will not be deleted if it is being used in another table or views.

eg below, the department table is being referenced by the employee table, you can not frop the table.
```sql
CREATE TABLE employee(
	emp_id INT PRIMARY KEY,
	first_name VARCHAR(50) NOT NULL,
	last_name VARCHAR(50) NOT NULL,
	gender CHAR(1),
	dept_id INT NOT NULL,
	    
    FOREIGN KEY (dept_id) REFERENCES department(dept_id)
);
	
-- the department table is being referenced by the employee table using the foreign key
CREATE TABLE department (
	dept_id INT PRIMARY KEY,
	dept_name VARCHAR(100) NOT NULL
);
```

To delete a table that is being referenced, use the `CASCADE` parameter.

now the department table can be deleted with the script below:
```sql
DROP TABLE  department CASCADE;
```

You can delete multiple tables by seperating them with a comma:
```sql
DROP TABLE IF EXISTS table1, table2;
```

## Truncate Table
---
([back to table of content](#table-of-content))

Truncate is used to delete data from a table.
```sql
TRUNCATE TABLE table_name;
```

`DROP` can be used as well, however `TRUNCATE` is better as it:
1. can delete huge data-sets.
2. is faster than `DROP`

You can use `RESTART IDENTITY` to delete all data and reset the sequence linked to the `id` column

```sql
TRUNCATE TABLE table_name RESTART IDENTITY;
```

If data is linked to another table, the truncate command shall not delete data. You can use `CASCADE` delete the data from the table and the data linked to the other table.

```sql
TRUNCATE TABLE table_name CASCADE;
```

## Alter Table
---
([back to table of content](#table-of-content))

The `ALTER TABLE` statement changes the structure of a table.

example of the table below:

```sql
create table employee (
    id int primary key not null,
    first_name character varying(50) not null,
    last_name character varying(50) not null,
    gender character(1),
    birthdate date,
    salary integer not null
)
```

Add a column to a table:
```sql
alter table employee add column new_col varchar(50);
```

Delete column from table:
```sql
alter table employee drop column col cascade;
```

Change data type of a column
```sql
alter table employee alter column type int 
```

Rename a column:
```sql
alter table table_name rename column old_name to new_name

--- example
alter table employee rename column birthdate to dob;
```

Rename a table
```sql
alter table table_name rename to new_table_name;

-- example
alter table employee rename to employee_new;
```

Add a `not null` constraint to a column:
```sql
alter table table_name alter column column_name set not null

-- example
alter table employee alter column dob set not null
```

remove/drop `not null` constraint
```sql
alter table table_name alter column column_name drop not null

-- example
alter table employee alter column dob drop not null
```

add a check constraint to a table and all its children ***(revisit)***:
```sql
ALTER TABLE employee ADD CONSTRAINT first_name CHECK (char_length(first_name) > 1);
```

remove a check constraint from a table and all its children ***(revisit)***:
```sql
ALTER TABLE employee DROP CONSTRAINT first_name;

ALTER TABLE ONLY employee DROP CONSTRAINT first_name; -- remove check constraint from one table only
```

Add primary key to table:
```sql
ALTER TABLE employee ADD PRIMARY KEY (id);
```

move a table to a different tablespace:
```sql
ALTER TABLE employee SET TABLESPACE emptablespace;
```

move a table to a different schema:
```sql
ALTER TABLE myschema.employee SET SCHEMA yourschema;
```


## Insert Data
---
([back to table of content](#table-of-content))

To insert data to table use the below statement:

```sql
insert into table_name (col1, col2, col3)
values
("value 1", "value 2", "value3");

-- example
insert into employee 
(id, first_name, second_name, gender, dob, salaries)
values
(1, 'Tony', 'Stark', 'm', DATE '1964-03-10', 100000)
```

note:
- use single quotes `'` when inserting strings. double quotes `"` will generate errors
- Dates are inserted in the `YYYY-MM-DD` format ie `DATE '1995-03-12'`
- If the table has a `SERIAL` column, Postgres will automatically generate a sequence number for the serial column. No need to specify a value for that column in the INSERT statement.
- `returning` returns the inserted data after the the insert quert has been executed 
```sql
-- returns all values after insertion
insert into table (col1, col2)
values
('col 1', 'col 2')
returning *;  

-- return specific column after insertion
insert into table (col1, col2)
values
('col 1', 'col 2')
returning col1; 
```

## Upsert Data
---
([back to table of content](#table-of-content))

`Upsert` is an `Insert`/`update` operation, i.e. It inserts values, however if the data already exists, it is updated.

## Update Data
---
([back to table of content](#table-of-content))


## Delete Data
---
([back to table of content](#table-of-content))

## Select Statement
---
([back to table of content](#table-of-content))

### Select Where
([back to table of content](#table-of-content))

### Select Group by
([back to table of content](#table-of-content))