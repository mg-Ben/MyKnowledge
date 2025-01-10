---
tags:
  - Useful_tools
---
# Core principles
The _crontab_ is used to schedule a [[#Task/Job]] in most [[UNIX|UNIX-like]] [[Operating System|Operating Systems]]. In other words, we can specify certain time instants throughout the day at which those tasks will be executed.
## Task/Job
A command to run.
## CronTab
The term _crontab_ stands for Cron Table, and is a file with a table-like structure where fields are separated by white space.
### Syntax
```crontab
crontab [options]
* * * * * <command>
* * * * * <path/to/script>
```
Each row is a [[#Task/Job]].
Each `*` represents, respectively, the exact time instant at which the command or Script will be executed:

|        | 1st    | 2nd   | 3rd      | 4th   | 5th      |
| ------ | ------ | ----- | -------- | ----- | -------- |
|        | *      | *     | *        | *     | *        |
| ID     | Minute | Hour  | Day-Date | Month | Day Name |
| Values | 0-59   | 0 -23 | 1-31     | 1-12  | 0-6      |
_NOTE: Day Names 0-6 begin with Sunday._
#### Example
We want to run `echo "Hello world"` on `Any_month 15th Any_hour:30`. In that case:
```crontab
30 * (<- at any hour) 15 * (<- on any month) * (<- on any week day) echo "Hello world" 
```
## Cron Daemon
The _crontab_ works like a [[Linux Service]].
# Hands on
## Edit [[#CronTab]] file
```shell
crontab -e
```
## See [[#CronTab]] file
```shell
crontab -l
```
