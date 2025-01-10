---
tags:
  - Databases
---
# Core principles
An Open-Source database **system** for managing [[Databases#Relational database|relational databases]].
## pg_hba.conf
Typically stored under `/etc/postgresql` on [[Linux]] installations.
The PostgreSQL configuration file. Here you set mainly **authentication settings**.
## .pgpass
Used in order to store passwords for databases. If doesn't exist, you can create one manually and attach to it `600` permissions with [[GNU#chmod (Change file or directory permissions)|chmod]].
- Located in [[Linux#/home/username|~]] path for Linux
Generic syntax:
```
<postgresql_hostname>:<postgresql_port>:<database>:<user>:<password>
```
# Hands on
## Install PostgreSQL
### Linux
#### APT
[[Linux#APT#Automatically downloading .deb package|Install postgresql package]] only with:
```shell
sudo apt install postgresql
```
## Commands

### psql commands
They server for interacting with PostgreSQL software.
#### Log in to PostgreSQL
[[Linux#Switch user|Switch to postgres user]] and run:
```shell
psql
```
#### Dump database into .sql file
```shell
pg_dump -U <username> -h <postgresql_hostname (e.g. localhost)> <database>
```
You will be prompted to enter a password. If you don't want to enter any password, configure the password for this database in [[#.pgpass]].
#### Dump table into .sql file
```shell
pg_dump -U <username> -h <postgresql_hostname (e.g. localhost)> --table <table> <database>
```
You will be prompted to enter a password. If you don't want to enter any password, configure the password for this database in [[#.pgpass]].
#### Dump .sql file into table
Firstly, [[#Create database|create a database]] where you will create the inner table. Then, [[Linux#Switch user|switch to postgres user]] and run this in [[GNU#Bash|bash]] (without `sudo`!):
```shell
psql -U postgres -d <database> -f </path/to/.sql>
```
You must place your `.sql` file in a folder where `postgres` user has got permissions, such as [[Linux#/home|/home]] directory.
#### List user and roles
```postgresql
\du
```
#### List databases
_Analogous to [[MySQL#Show databases]]_
```postgresql
\l
```
#### Create database
```SQL
CREATE DATABASE <database>;
```
#### Connect to a database
```postgresql
\c <database>
```
You can also connect to an existing database directly from [[Operating System#Shell|Shell]] with:
```shell
psql <database>
```
In this case, you may not need to change user to **postgres** user.
#### List tables
_Analogous to [[MySQL#Show tables]]_
```postgresql
\dt
```
#### Get table fields
```PostgreSQL
\d <table>
```
#### See table content
```postgresql
SELECT * FROM <table>;
```
#### Quit psql
```postgresql
\q
```
### SQL commands
Once you have connected to a PostgreSQL table, you can perform [[SQL#SQL Queries|SQL Queries]].
However, remember that PostgreSQL implements its own queries apart from the standard SQL queries: for more information about PostgreSQL queries, refer to [PostgreSQL: Documentation: 17: Chapter 7. Queries](https://www.postgresql.org/docs/current/queries.html). If interested in pattern matching in the [[SQL#WHERE]] clause, refer to [PostgreSQL: Documentation: 17: 9.7. Pattern Matching](https://www.postgresql.org/docs/17/functions-matching.html).