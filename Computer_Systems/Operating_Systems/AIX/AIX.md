---
tags:
  - Computer_Systems
  - Operating_Systems
---
# Core principles
It is a [[UNIX|UNIX-like]] [[Operating System]] developed by IBM.
## LPAR
_LPAR stands for Logical Partition_
It is a [[Virtualization#Guest Operating System|Virtual Machine]], but in IBM terminology. Each LPAR runs its own operating system. This can be any mainframe operating system; there is no need to run z/OS®, for example, in each LPAR. The installation planners may elect to share I/O devices across several LPARs, but this is a local decision.

The system administrator can assign one or more system processors for the exclusive use of an LPAR. Alternately, the administrator can allow all processors to be used on some or all LPARs. Here, the system control functions (often known as microcode or firmware) provide a dispatcher to share the processors among the selected LPARs. The administrator can specify a maximum number of concurrent processors executing in each LPAR. The administrator can also provide weightings for different LPARs; for example, specifying that LPAR1 should receive twice as much processor time as LPAR2.
## IBM terminology
- [[Operating System#CPU|CPU]] has no equivalent term in IBM
- [[Operating System#Core|Core]] is equivalent to [[Operating System#CPU|CPU or processor]] in IBM
- [[Operating System#Thread|Thread]] is equivalent to **Logical CPU or Logical Processor** in IBM
## Subsystem
_Refer to [[AIX Subsystem]]_
They are analogous to [[Linux Service|Linux Services]].
# Hands on
## Interesting commands
### lparstat (get [[#LPAR]] information)
Shows [[#LPAR]] information and utilization statistics. Example:
```
System configuration: type=Shared mode=Uncapped smt=8 lcpu=8 mem=2048MB psize=20 ent=0.25

%user   %sys  %wait  %idle physc  %entc  lbusy  vcsw phint  %nsp  %utcyc
-----   ----  -----  ----- -----  -----  -----  ---- -----  ----  ------
  0.0    0.0    0.3   99.7  0.00    0.0    0.2 1555868   39   150   33.33
```
### prtconf (get Hardware information)
Shows Hardware information. Example:
```
System Model: IBM,...
Machine Serial Number: 12345
Porcessor Type: PowerPC_POWER9
...
```
### shutdown
```shell
shutdown
```
Interesting flags:
- `-h now`: force shutdown now