# **postgresql-documentation**

# Overview
Personal documentation for postgreSQL

## Commands
-"\q" : exit
- "\l" : view list of all databases
- "\c database" : Connect to database
- "\du" : database users
- \d : 

## SQL commands
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

Create a table (eg person)
```
CREATE TABLE person(
    id int,
    fname VARCHAR(100),
    lname VARCHAR(100),
    date_of_birth TIMESTAMP,
)
```