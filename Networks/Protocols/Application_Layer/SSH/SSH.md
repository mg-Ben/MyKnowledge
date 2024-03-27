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
It is an alternative to [[Telnet]].
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
The encryption algorithm can be one of the following: [[Cybersecurity#Example algorithms]].
The output of this command is both the public key and the private key as files (the public key one would have the ```.pub``` extension, whereas the private has no visible extension). Refer to [[Cybersecurity#Authorization]] to know more.
As in SSH there is no registering process (only log in) in a rigorous sense, permissions must be granted to certain users to prevent anyone from accessing the SSH server. Because of that, in server side we must define the ```./ssh/authorized_keys``` list, where we set which public keys have access and which not.
#### Configuration
You will find the ```~/.ssh/config``` file, where all SSH configurations are set. If the file does not exist, you can [[UNIX#touch (Create new file)|create it]]. This configuration file help you not specify all the flags in [[#ssh (Connect remotely to the SSH server)|ssh command]] when connecting to the remote server, but configure them in a single configuration file.
##### Hostname
Either [[IP#IP address|IP address]] of the destination device or the [[DNS|domain name]].
##### Port
Destination port of the SSH server. Equivalent to specify ```-p``` flag in [[#ssh (Connect remotely to the SSH server)|ssh command]].
##### User
Some SSH Servers expect users to specify a username. Equivalent to specify ```username@...``` in [[#ssh (Connect remotely to the SSH server)|ssh command]].
##### ProxyJump
When you connect to a remote server, an intermediate machine can exist between client and server (i.e. a [[Proxies|Proxy]]. Here is where you can set the proxy information.
##### LocalForward
Once you are connected to a remote SSH server, you can mirror your [[Internet#Interact with running ports|interaction with some local port]] on your client machine to the interaction of the SSH server with its port. In other words, for instance, if you are connected to the remote SSH server and you interact with your ```localhost:8080``` port, actually, instead of interacting with your ```localhost:8080```, you are interacting with ```8080``` port on the remote server.

![[LocalForwardIdea.png]]

You can also configure which port on your local corresponds to which port on the remote (e.g. ```localhost:A``` to ```localhost:B``` on remote).
##### DynamicForward
When you connect to a remote SSH server, sometimes you need to connect to a certain port in that server. In that case, you could use [[#LocalForward]], but sometimes you need to use your own local ports. In that case, you could directly connect to ```IP_remote:Port_remote```. However, your [[Internet#Interact with running ports|interaction with that port]] might require the use of a web browser, which doesn't typically allow the connection to SSH servers directly. Therefore, a [[Proxies#SOCKS5|Proxy SOCKS]] must be configured on your local machine to connect to the SSH server through an intermediate proxy by a web browser, but also on your configuration SSH file. The Proxy SOCKS is just a process running on your localhost which acts as a proxy.
With DynamicForward, you can set which port to launch the proxy SOCKS on. Once set, the proxy SOCKS is executed when running ```ssh``` command. Then, you can configure your web browser so as to use that proxy. You can now connect to any port on remote SSH server.

![[DynamicForwardIdea.png]]

##### IdentityFile
With this configuration you can specify the path and name of the keyfiles. Equivalent to ```-i``` flag in [[#ssh (Connect remotely to the SSH server)|ssh command]].
#### Commands
##### ssh (Connect remotely to the SSH server)
You can connect remotely to a SSH server by:
```shell
ssh <destination>
```
The value of ```<destination>``` can be:
- The destination [[IP#IP address|IP address]] or [[DNS]]
- The username + the destination [[IP#IP address|IP address]] or [[DNS|domain name]], separated by ```@``` (e.g. ```bmartin@1.2.3.4```)
Flags:
- ```-p <PORT>```: specify destination port
- ```-i <keyfile>```: specify the name of the [[#keyfile]].
##### scp (Secure Copy)
You can copy files from one machine to a remote machine securely with:
```shell
scp <filename> destination:<destination_directory>
```
Or you can copy directories from one machine to a remote machine securely with:
```shell
scp -r <path> destination:<destination_directory>
```
Tips:
- **Don't use scp with sudo**, as you can find errors like:
```
ssh: Could not resolve hostname X: Temporary failure in name resolution
lost connection
```
- You must have permissions on ```<destination_directory>```


