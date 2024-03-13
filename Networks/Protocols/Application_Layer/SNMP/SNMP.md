---
tags:
  - Networks
  - Protocols
  - Application_Layer
---
# Core principles
## Definition
SNMP stands for _Simple Network Management Protocol_
An [[OSI Model|Application Layer]] protocol for **managing** network devices. It consists of:
- An **agent**: network device to be monitored
- A **manager**: network device which monitors the agent

The SNMP agent will have a list of [[#OIDs]] that exposes to SNMP manager. The SNMP manager will have a list of [[#OIDs]] that requests to the SNMP agent. Those lists might not be the same. The SNMP manager will only request the OIDs It wants depending on what information It wants to monitor.
## Typical OSI layer configuration
SNMP often operates over [[UDP]], because operating over [[TCP]] implies a traffic overload when setting the communication. SNMP usually uses port 161 or 162.
SNMP also operates over [[ASN.1]], [[DES]] or [[SHA-1]] for [[OSI Model|Presentation Layer]].
## SNMP communities
Used to manage different sets of devices in large networks. There exist two types:
- **Read-Only community**: the manager can only view agent data, but not change it
- **Read-Write community**: the manager can both view and modify agent data
## Versions
### v1
Simple, with basic operations (GET, SET, TRAP) and limited security (no encryption). No support for bulk retrieval. Uses **communities**.
### v2
More efficiency, introduced GetBulkRequest to retrieve large amounts of data and error signaling.
### v2c
Uses **communities**, but they are passed in cleartext.
### v3
Improves security and architecture. **Doesn't use communities**.
## SMI
_SMI stands for Structure Management of Information_
In SNMP, there must exist a way to define what a SNMP manager can monitor from an agent, or what a SNMP agent can expose to the manager. For example, the CPU usage, the chassis temperature, the network interface load, the number of processes... Those attributes are defined in a universal standard defined by SMI.
SMI is a **set of rules** that defines the structure and organization of management information. It is used by SNMP to organize the data that can be monitored. In SMI you can find the [[#OIDs]] and  [[#MIB]] key concepts.
### OIDs
_OID stands for Object IDentifier_
In SNMP, there exists an universal tree where all the attributes available to be monitored and exposed through SNMP protocol are defined. This is called the **OID tree**, and each attribute (also known as object) has got its own identifier.
The OID of a certain attribute (e.g. the attribute "CPU usage") has got a format like ```.1.3.6.1.4.1.56``` (invented example). This format means that the object has got the ID 56 (last number), whose parent object ID is 1, whose parent object ID is 4, whose parent object ID is 1 and so on, because SMI defines objects in a tree structure.
Each number (1, 3, 6, 1, 4, 1...) has got their own meaning: the first 1 stands for ```iso```, the 3 is ```org```, the following 6 means ```dod``` (Department of Defense of EEUU), the following 1 is ```internet```...
##### Example
This screenshot has been obtainer from [[#Good references]] - SNMPLink.
![[OID-tree-example-snmplink.png]]
#### MIB
_MIB stands for Management Information Base_
A MIB is a subtree inside the universal OIDs tree. In the example above, you can find ```mib-2``` subtree, which is a MIB.
All the objects in SNMP belong to a certain MIB.
Sometimes, a company wants to add some attributes to the universal OIDs tree. For example, CISCO might want to add their own OIDs to manage their own CISCO devices products (such as routers or switches). To do so, CISCO must arrange with [[Internet#IANA]] which OIDs are available to use. Those OIDs are defined inside a MIB, and then that MIB, with its OIDs, is added to the universal OIDs tree. Therefore, you can find lots of MIBs from different manufacturers on the OIDs universal tree.
In practice, MIBs are ```.mib``` or ```.txt``` files with a content which looks like:
![[MIB-content-example.png]]
In the example above, you can find the objects that are defined inside that MIB (hrSystem, hrStorage, hrDevide...) and their data types.
It's very common that some MIBs depend on another MIBs. In section ```IMPORTS``` you can find the MIB dependencies:
![[MIB-content-dependency-example.png]]
##### Example
You can explore MIBs and their contents either on Circitor or SNMPLink (refer to [[#Good references]]).
![[MIBs-example-circitor.png]]
#### SNMP Operations
##### GET
##### GETBULK
##### WALK
#### SNMP Data Types
##### SNMP Table
Has got an entry object, which represents a table row. That entry often contains an index object.
###### Example
![[SNMP-table-example.png]]
![[SNMP-table-example-index.png]]
# Hands on
## How to deploy a SNMP agent
### With snmpd service
## How to deploy a SNMP manager
### With SNMP Prometheus Exporter
Refer to [[SNMP Exporter]].
### With command-line tools
You can make SNMP requests against a SNMP agent to get some information through command-line tools.
#### snmpwalk
Perform a [[#WALK|SNMP walk]] operation:
```shell
snmpwalk -v <version_to_use> -c <community_name> <agent_IP> <OID_from>
```
If not ```<OID_from>``` specified, it performs walk operation from highest OID tree level (i.e. ```.1 = iso```).
#### snmpget
## Good practices
Most SNMP devices can be managed through custom enterprise MIB files depending on which device or the manufacturer. This means that you should analyze specific MIBs for the Internet devices to be managed instead of the most general ones. All these specific-enterprise MIBs lie under ```1.3.6.1.4.1 (iso.org.dod.internet.private.enterprise``` subtree. For example, all the CISCO MIBs can be found under ```1.3.6.1.4.1.9.9```.  Therefore, a good practice when analyzing some device (such as [[Firewalls]], [[Balancers]], [[Switches]] or whatever) is directly performing a [[#WALK]] from the specific OID.
Here you have a useful command to process through [[AWK]] and [[UNIX#(Pipes)]] the output of ```snmpwalk```. For each OID subtree under the OID you want, this command will count the number of OIDs that are inside each one of those subtrees. For example, you want to count how many OIDs the SNMP agent exposes for each MIB in CISCO:
- ```1.3.6.1.4.1.9.9.1```
- ```1.3.6.1.4.1.9.9.2```
- ```1.3.6.1.4.1.9.9.3```
- ```1.3.6.1.4.1.9.9.4```
- ...

```shell
snmpwalk [whatever] 1.3.6.1.4.1.9.9 | awk -F '.' '{dict[$4] += 1}END{for(key in dict){print(key";"dict[key])}}'
```
# Good references
- To explore the [[#OIDs]] tree: [SNMPLink](http://www.snmplink.org/) (Go to Online-MIB Section)
- To get information of a specific OID (e.g. get the MIB of an OID), refer to:
```
oidref.com/<OID> #Example: oidref.com/1.3.6.1.4.1.1
```
- To download MIBs: [Circitor](https://www.circitor.fr/Mibs/Mibs.php)