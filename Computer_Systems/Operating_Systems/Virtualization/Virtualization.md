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
#### Shared folders
By default, some Guest OS can't access to the Host OS files. However, you may need to send files from **Host** to **Guest** or the other way round. To do so, you have two options:
- `Drag and Drop` in VMware settings > Settings of Guest OS > Virtual Machine Settings > Options > Guest Isolation > Enable drag and drop. However, that option might be missing. The menu where you should find it is this one:
![[vmware-settings-options.png]]
- If `Drag and Drop` is missing, you can opt for `Shared Folders`. For this purpose, go to VMware settings > Settings of Guest OS > Virtual Machine Settings > Options > Shared Folders. You can select a path inside your [[#Host Operating System]] that will be available inside your [[#Guest Operating System]]. If after turning on Guest OS and your Guest OS is Windows (such as [[Windows Server 2003]]) you can't find the shared folder, ensure you have selected the option _Map as a network drive in Windows guests_