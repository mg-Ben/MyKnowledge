---
tags:
  - Programming_Languages
---
# Core principles
## Definition
It is a Script with [[GNU#Bash]] commands (i.e. both GNU commands and UNIX-standard commands). Its extension is ```.sh```.
## Binary
A special [[#Definition|Bash Script]] which can be turned into a command.
# Hands on
## Run Script
```shell
./<your-script>.sh
```
Here we are running a command that is equivalent to:
```shell
/bin/bash <your-script>.sh
```
So bash is itself a command (i.e. a binary ```.sh``` file) that serves for running Bash Scripts, and you can find it in [[Linux#/bin]] directory as any other command you imagine.

However, you can turn your Script into a command only if your ```.sh``` fulfils certain requirements (for example, to be a binary file) by [[UNIX#UNIX-CLI standard commands#mv (Move or rename files)|moving it]] or [[UNIX#UNIX-CLI standard commands#cp|copying it]] to [[Linux#/bin]] directory.
Once you have moved your binary file to ```/bin``` directory, your command is available. To run it:
```shell
<your-script>
```
