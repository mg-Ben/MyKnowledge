---
tags:
  - Networks
  - Internet
  - Protocols
  - Application_Layer
---
# Core principles
_SSH stands for Secure SHell, but it is considered a protocol_
Is a cryptographic network [[OSI Model|Application Layer]] protocol based on [[Internet#Client-Server communication|Client-Server communication]] that allows us to access to a remote machine through a [[Operating System#Shell|Shell]] to run commands inside it, as long as that machine is running a SSH server to accept connections.
# Implementations
SSH protocol is implemented on different programs and [[Operating System|Operating Systems]].
## PuTTY
## OpenSSH
The implementation of SSH in [[UNIX|UNIX-like]] [[Operating System|Operating Systems]]. Provides several commands such as ```ssh```, ```scp```, ```sftp```, ```ssh-keygen```... However, OpenSSH is also available on Windows, which is not UNIX-like.
### Hands on
Every OpenSSH configuration is inside ```.ssh``` directory, which is typically located on [[Linux#/home|user's home directory]] (```/home/user = ~```). 
#### SSH public and private keys
Whenever you want to connect to a target SSH server, you need to create a **SSH public and private key pair**. Both are actually files on your system that you can generate through the OpenSSH ```ssh-keygen``` command.
```shell
ssh-keygen -t <encryption_algorithm> -C "usermail"
```
The encryption algorithm can be 
#### Configuration
You will find the ```~/.ssh/config``` file, where all SSH configurations are set. If the file does not exist, you can [[UNIX#touch (Create new file)|create it]].
##### Hostname
Either [[IP#IP address|IP address]] of the destination device or the [[DNS|domain name]].
##### Port
Destination port of the SSH server.
##### User
##### ProxyJump
##### LocalForward
##### Dynamic Forward
##### IdentityFile


