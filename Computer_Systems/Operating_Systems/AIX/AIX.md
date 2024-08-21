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
### How cores are shared for LPARs?
- The machine has got `N` physical [[#IBM terminology|CPUs]] (see [[#lparstat (get LPAR information)|lparstat -i]] > Maximum Physical CPUs in system)
- From those CPUs, we choose a subset and that subset is the cores that we enable to share between the [[#LPARs]] (see [[#lparstat (get LPAR information)|lparstat -i]] > Shared Physical CPUs in system)
- The number of CPUs we have assigned to a [[#LPAR]] is the desired virtual CPUs. We call **virtual CPUs** the CPUs that are inside a LPAR (see [[#lparstat (get LPAR information)|lparstat -i]] > Desired Virtual CPUs)
- AIX allows to assign fractions of a CPU to a LPAR (e.g. if your LPAR has got 3.25 CPUs, it means that it has 3 CPUs plus 0.25 of another CPU, which means a 25% of processing time of that CPU). See [[#lparstat (get LPAR information)|lparstat -i]] > Entitled Capacity or [[#lparstat (get LPAR information)|lparstat]] > ent field to get this number of assigned CPUs
	- Here there are two modes: _Capped_ and _Uncapped_. The _Uncapped_ mode allows the LPAR to have more CPUs than Entitled Capacity (between a Minimum and Maximum; see [[#lparstat (get LPAR information)|lparstat -i]] > Minimum/Maximum Capacity)
- The number of [[#IBM terminology|Logical CPUs]] can be obtained from [[#lparstat (get LPAR information)|lparstat]] > lcpu field. it is equal to the number of CPUs of this LPAR (i.e. virtual CPUs) times the number of [[Operating System#Thread|threads]] that one CPU can handle (see [[#IBM terminology]]).

## IBM terminology
- [[Operating System#CPU|CPU]] has no equivalent term in IBM
- In IBM, we say [[Operating System#CPU|CPU or processor]] instead of [[Operating System#Core|Core]]
- In IBM, we say **Logical CPU or Logical Processor** instead of [[Operating System#Thread|Thread]]
In IBM, we use SMTX to denote that each CPU can handle X threads:
- SMT2 means that each CPU can handle up to 2 threads
- SMT4 means that each CPU can handle up to 4 threads
- SMT8 means that each CPU can handle up to 8 threads
## Subsystem
_Refer to [[AIX Subsystem]]_
They are analogous to [[Linux Service|Linux Services]].
# Hands on
## Interesting commands
### smtctl
Get [[Operating System#Thread|SMT]] information
```
smtctl
```
### lparstat (get [[#LPAR]] information)
Shows [[#LPAR]] information and utilization statistics. Example:
```
System configuration: type=Shared mode=Uncapped smt=8 lcpu=8 mem=2048MB psize=20 ent=0.25

%user   %sys  %wait  %idle physc  %entc  lbusy  vcsw phint  %nsp  %utcyc
-----   ----  -----  ----- -----  -----  -----  ---- -----  ----  ------
  0.0    0.0    0.3   99.7  0.00    0.0    0.2 1555868   39   150   33.33
```
Interesting flags:
- `-i`: show detailed information
### prtconf (get Hardware information)
Shows Hardware information. Example:
```
System Model: IBM,...
Machine Serial Number: 12345
Porcessor Type: PowerPC_POWER9
...
```
### lsdev (list system devices)
_Does not work well if the command runs inside a LPAR_
```
lsdev
```
Interesting flags:
- `-Cc <device>`: get specific information of some device (e.g. `-Cc processor`)
### shutdown
```shell
shutdown
```
Interesting flags:
- `-h now`: force shutdown now