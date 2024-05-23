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
The [[Internet#LAN|LAN portion]] that this interface belongs to. When a packet is sent/received to/from the network interface, the destination/source IP address is checked to determine whether that packet comes from/goes to another LAN portion.
- If the other device belongs to the same LAN portion, the communication can occur directly
- Otherwise, a [[Internet#Gateways|gateway]] is needed to route traffic
### First-hop router
When a device wants to communicate to another that doesn't belong to the same network portion, the packet must be sent to a gateway.
## IP address range
The IP address range is essentially defined by setting a **prefix**, i.e. some bits from left to right that must be fixed. For example:
`5.0.0.0/8` sets that the first 8 bits (`00000101`) must be fixed:
```
00000101.00000000.00000000.00000000
[------]
```
The remaining bits are 0 in this notation, meaning that they are variable.
- Therefore, the IP addresses that fall in this range would be `5.0.0.1`, `5.0.0.2`, `5.0.0.3`...`5.255.255.255`
- The IP address that results of setting all variable bits to 1 is called **Broadcast address** (in the example, `5.255.255.255`)
In addition to `5.0.0.0/8` notation, we can find other notation that means the same; the **subnet mask** notation:
```
/8 = The first 8 bits must be fixed = The mask is:
11111111.00000000.00000000.00000000 = 255.0.0.0
```
The mask is another IP address that represents the fixed bits with ones, because if we do a [[Binary numeral system#Bitwise|Bitwise]] [[Binary numeral system#AND|AND]] of any IP address with the mask, the resulting bits in those places where mask is 1 will remain equal. Example:
```
      12. 63.235.67  = 00001100.00111111.11101011.01000011
AND  255.255.  0. 0  = 11111111.11111111.00000000.00000000
----------------------------------------------------------
       12. 63. 0. 0  = 00001100.00111111.00000000.00000000
```
### Class based
In the past, the IP address ranges were defined by **classes**:
- **Class A** means that the mask is `255.255.255.0`
- **Class B** means that the mask is `255.255.0.0`
- **Class C** means that the mask is `255.0.0.0`
### CIDR
_CIDR stands for Classless Inter-Domain Routing_
It is a more flexible notation, not limited to classes. It allows us to define any number of fixed bits. For example:
`5.0.0.0/8`