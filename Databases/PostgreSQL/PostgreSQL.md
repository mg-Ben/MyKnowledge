---
tags:
  - Databases
---
# Core principles
An Open-Source database **system** for managing [[Databases#Relational database|relational databases]].
## pg_hba.conf
Typically stored under `/etc/postgresql` on [[Linux]] installations.
The PostgreSQL configuration file. Here you set mainly **authentication settings**.
# Hands on

## psql commands
They server for interacting with PostgreSQL software.
### Log in to PostgreSQL
[[Linux#Switch user|Switch to postgres user]] and run:
```shell
psql
```
## List user and roles
```postgresql
\du
```
## List databases
_Analogous to [[MySQL#Show databases]]_
```postgresql
\l
```
## List tables
_Analogous to [[MySQL#Show tables]]_
```postgresql
\dt
```
## Connect to a database
```postgresql
\c <database>
```
## Quit psql
```postgresql
\q
```