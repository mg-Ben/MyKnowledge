---
tags:
  - Networks
  - Internet
  - Protocols
  - Network_Layer
---
# Core principles
## Definition
A [[OSI Model|Network Layer]] protocol.
## Network Interfaces
Each network interface has got the following data:
### IP address
One network device (e.g. your computer) on Internet has got several IP addresses. You can list them with ```ipconfig``` (Windows) or ```ifconfig``` ([[Linux]]) commands, that lists all the Network Interfaces.
Each Network Interface represents a way to reach your machine on Internet.
For instance, if you are wired-connected to Internet, you will have one interface to reach your machine through Ethernet. If you also have a WiFi antenna, you will be reachable through the interface that represents the antenna, and so on. Besides, you might be running a [[Virtualization#Guest Operating System|Guest Operating System]] inside your [[Virtualization#Host Operating System|Host Operating System]]. In that case, you can assign a network interface to the [[Virtualization#Guest Operating System|Guest Operating System]] to make it directly reachable from outside Internet. You can also have [[Docker#Docker container|Docker containers]] running on your system, and each one can have their own network interface (i.e. IP address).
There are some standard IP address values with their own meaning. For example:
- `0.0.0.0` means "All network interfaces"
- `127.0.0.1` means "localhost"
#### Format
##### IPv4
It consists of four [[Binary numeral system#Byte|Bytes]] separated by ```.```:
```
01101011.00110000.11111001.00001010
```
Each byte represents an [[Binary numeral system#Unsigned integer values|Unsigned integer value]]. Therefore, it can be represented like:
```
107.48.249.10
```
### Ethernet address
The physical address of the interface.
### Netmask
The LAN portion that this interface belongs to. When a packet is sent/received to/from the network interface, the destination/source IP address is checked to determine whether that packet comes from/goes to another network portion. If false, the other device belongs to the same network portion and the communication can occur directly. Otherwise, a gateway is needed to route traffic.
### First-hop router
When a device wants to communicate to another that doesn't belong to the same network portion, the packet must be sent to a gateway.