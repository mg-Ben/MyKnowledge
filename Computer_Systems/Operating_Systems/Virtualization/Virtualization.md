---
tags:
  - Computer_Systems
  - Operating_Systems
---
# Core principles
## Definition
## Host Operating System
The [[Operating System]] inside which you are running the [[#Guest Operating System]] or a [[Docker#Docker container|Docker container]].
## Guest Operating System
The [[Operating System]] which is running inside the [[#Host Operating System]]. For example, you can run [[Distributions#Ubuntu]] inside Windows through VMware.
![[virtualization-guestOS-example.png]]
# Hands on
## VMware
A software product that allows us to virtualize [[Operating System]]. For example, you can download-install any Operating System inside your system as [[#Guest Operating System]] and play with it.
### Handy configurations
#### Network Adapter
##### Bridge mode
The Virtual Machine will act as a separate machine in the same network as the [[#Host Operating System]].
It will be assigned its own [[IP]] address.

![[bridged-net-mode.png]]
###### Hyper-V LAN
To connect to that Virtual Machine from your Host OS, in your [[#Host Operating System]] you should see something like a `vEthernet (Default Switch)` adapter when running [[IP#IP address|ipconfig or ifconfig]]. That adapter is the interface that allows you to connect to your Virtual Machines from your Host OS (see [[Internet#LAN portion]], Hyper-V LAN and virtual Ethernet).
- By running `ipconfig` or `ifconfig` you can see the [[IP#IP address range]] of Hyper-V LAN. For example:
```
ipconfig output:

Ethernet adapter vEthernet (Default Switch):

   Connection-specific DNS Suffix  . :
   Link-local IPv6 Address . . . . . : abcd::8dd7:ed66:121f:c37f%25
   IPv4 Address. . . . . . . . . . . : 34.124.72.1              <--------------
   Subnet Mask . . . . . . . . . . . : 255.255.240.0            <--------------
   Default Gateway . . . . . . . . . :
```
In this case, the virtual Ethernet interface inside our Host OS has got `34.124.72.1` IP address and a subnet mask of `255.255.240.0 = 1111111.11111111.11110000.00000000`.
- The subnet says that the first 20 bits are fixed.
- We have an example of IP address that is `34.124.72.1 = 00100010.01111100.01001000.00000001`
- As the first 20 bits are fixed: `00100010.01111100.0100XXXX.XXXXXXXX`. The subnet is `00100010.01111100.01000000.00000000 = 34.124.64.0/20` and the broadcast address is `00100010.01111100.01001111.11111111 = 34.124.79.255`
###### Replicate physical network state
- **Network Adapter State**: The VM’s network adapter will reflect the state of the host’s physical network adapter. This includes the link state (whether the network cable is connected or disconnected), the speed, and the duplex settings
- **Link State Awareness**: If the host’s physical network adapter is disconnected from the network (e.g., if the network cable is unplugged), the VM’s network adapter will also reflect this disconnection, simulating the same network conditions within the VM
- **Use Case**: This is useful for testing and development scenarios where the network conditions of the physical environment need to be mirrored in the virtual environment
#### Shared folders
By default, some Guest OS can't access to the Host OS files. However, you may need to send files from **Host** to **Guest** or the other way round. To do so, you have two options:
- `Drag and Drop` in VMware settings > Settings of Guest OS > Virtual Machine Settings > Options > Guest Isolation > Enable drag and drop. However, that option might be missing. The menu where you should find it is this one:
![[vmware-settings-options.png]]
- If `Drag and Drop` is missing, you can opt for `Shared Folders`. For this purpose, go to VMware settings > Settings of Guest OS > Virtual Machine Settings > Options > Shared Folders. You can select a path inside your [[#Host Operating System]] that will be available inside your [[#Guest Operating System]]. If after turning on Guest OS and your Guest OS is Windows (such as [[Windows#Windows Server 2003|Windows Server 2003]]) you can't find the shared folder, ensure you have selected the option _Map as a network drive in Windows guests_