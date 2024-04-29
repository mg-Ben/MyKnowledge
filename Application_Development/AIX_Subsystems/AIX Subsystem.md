---
tags:
  - Application_Development
---
# Core principles
Analogous to [[Linux Service]].
# Hands on
## List current subsystems
```shell
lssrc -a
```
## Create a new subsystem
```shell
mkssys -s <service_name> -p <path_to_binary_file> -u <user_id>
```
Example of `<user_id>`: `root` (it can be whatever we want).
## Start a subsystem
```shell
startsrc -s <service_name>
```
## Restart a subsystem
```shell
refresh -s <service_name>
```
## Stop a subsystem
```shell
stopsrc -s <service_name>
```
### Force stop
Retrieve the PID (Process ID) of your subsystem with [[#List current subsystems|lssrc]] and:
```shell
kill <pid>
```
## Check subsystem logs
Go to:
```shell
cat /etc/syslog.conf
```
And look for `*.debug` line. If you don't find any `*.debug` entry or it is commented, comment it out.
Example:
```
*.debug              /var/log/syslog.debug100k.out  rotate size 100k files 4
```
Then, [[#Restart a subsystem|refresh]] `syslog.conf` by refreshing `syslogd` subsystem:
```shell
refresh -s syslogd
```



# References
- [AIX and daemon service - Unix & Linux Stack Exchange](https://unix.stackexchange.com/questions/563404/aix-and-daemon-service)