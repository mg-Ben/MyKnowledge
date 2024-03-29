---
tags:
  - Useful_tools
---
# Core principles
Here you will find some useful commands that are not universal for every [[UNIX]]-like [[Operating System]] or are not part of [[GNU]] project.
# nmap
_nmap stands for Network Mapper_
Very useful for pentesters. With this command you can know what [[Internet#Port]] (processes) are currently opened on a remote machine:
```shell
nmap <targetIP>
```
Flags and options:
- ```-sV```: consider also non-standard ports. E.g.: ```nmap -sV <targetIP>```
# ping
Useful to check the destination's machine reachability and test connectivity. It uses [[ICMP]] protocol.
```shell
ping <targetIP>
```
# telnet
Connect your keyboard to a [[Internet#Socket]]:
```shell
telnet <targetIP>
```
We can log into a remote machine by telnet and get access to its [[Operating System#Shell]].