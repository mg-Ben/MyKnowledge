---
tags:
  - Computer_Systems
  - Operating_Systems
---
# Core principles
It is like [[Linux]], but with enhanced security features. For example, [[Linux#Super/Root users]] might not have access to apply some configurations, and there are strict policies that users and applications cannot override.
## SELinux Booleans
In SELinux, we can toggle security rules by setting some boolean variables to on/off.
## SELinux Security Context
In SELinux, every file, process and network port has got a **SELinux Security Context**.
# Hands on
## Get File SELinux Context
```shell
ls -Z <file>
```
The output is in the format: `user:role:type:level`
## Set File SELinux Context
```shell
chcon -t <TYPE> <file>
```
For example, you can set the `<TYPE>` to `bin_t` to configure `<file>` as a normal binary file.
## Get SELinux configuration
```shell
getsebool -a
```
## Set SELinux configuration
```shell
setsebool -P <variable> on
```
## Temporarily set SELinux in _Permissive Mode_
SELinux Permissive Mode allows us to disable SELinux restrictions temporarily. However, this setting is not persistent and will revert after system reboot.
```shell
setenforce 0
```
You can use this mode to debug and know when an application is not working properly due to SELinux's own restrictions.
## Set SELinux in _Enforcing Mode_
SELinux Enforcing Mode is the normal operation of SELinux.
```shell
setenforce 1
```