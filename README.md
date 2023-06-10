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

## **Installation:**
Linuc:
```bash
$ sudo apt install postgresql
```

Windows :
- download from : https://www.postgresql.org/download/windows/

## **PSQL Commands**
- `\q` : exit
- `\l` : view list of all databases (show databases)
- `\c database` : Connect to database (use db)
- `\du` : database users
- `\d` `\dt` : show tables
- `\d table` : describe table (desc table)
- `\dt table` : show table details

## **SQL commands**

## Create database
---
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

view database connection information
Create a table (eg person)
```
/conninfo 
```

## Create table
---
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
A temporary table is a table that lasts only in the duration of the session.
One you log out of the db session, it is deleted.