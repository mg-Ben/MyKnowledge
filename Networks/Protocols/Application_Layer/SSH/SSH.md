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
## Communication
### KeepAlive
A message that the SSH Client sends to SSH Server to keep the session alive.
The server may close the session after some elapsed time if does not receive information from client.
After `N` consecutive KeepAlive messages, if no response from server, the client closes the connection.
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
The default SSH server port is `22`.
##### User
Some SSH Servers expect users to specify a username. Equivalent to specify ```username@...``` in [[#ssh (Connect remotely to the SSH server)|ssh command]].
##### ProxyJump
When you connect to a remote server, an intermediate machine can exist between client and server (i.e. a [[Proxies|Proxy]]. Here is where you can set the proxy information.
##### LocalForward
_Used to access remote services locally (reverse of [[#RemoteForward]])_
Once you are connected to a remote SSH server, you can forward your [[Internet#Interact with running ports|interaction with some local port]] on your client machine to the interaction of the SSH server with its port. In other words, for instance, if you are connected to the remote SSH server and you interact with your ```localhost:8080```, the traffic will be forwarded to another port inside the remote host.

![[LocalForwardIdea.png]]


```Syntax
LocalForward your_local_port remote_address:remote_port
```
For the example above:
```
LocalForward 8080 localhost:9119
```
##### RemoteForward
_Used to expose local services to remote users (reverse of [[#LocalForward]])_
If you are connected to the remote SSH server and that remote server interacts with its ```localhost:9119```, the traffic will be forwarded to some port inside your local host.

![[RemoteForwardIdea.png]]

```Syntax
RemoteForward remote_port local_address:local_port
```
For the example above:
```
LocalForward 9119 localhost:8080
```
##### DynamicForward
When you connect to a remote SSH server, sometimes you need to connect to a certain port in that server. In that case, you could use [[#LocalForward]], but sometimes you can't use your own ports beacuse they are reserved to other things. In that case, you could directly connect to ```IP_remote:Port_remote```. However, your [[Internet#Interact with running ports|interaction with that port]] might be from a web browser, which doesn't typically allow the connection to SSH servers directly. Therefore, a [[Proxies#SOCKS5|Proxy SOCKS]] must be configured on your local machine to connect to the SSH server through an intermediate proxy by a web browser, but also on your configuration SSH file. The Proxy SOCKS is just a process running on your localhost which acts as a proxy.
With DynamicForward, you can set which port to launch the Proxy SOCKS on inside your local machine. Once set, the Proxy SOCKS is executed when you connect to the remote host by `ssh`. Then, you can configure your web browser so as to use that proxy. You can now connect to any port on remote SSH server.

![[DynamicForwardIdea.png]]

For instance, to configure Mozilla Web Browser to use the Proxy SOCKS, go to Settings and Network Settings. Then, configure where the Proxy SOCKS is running:

![[MozillaProxySOCKS.png]]

Once configured, connect to remote host by `ssh` to activate the Proxy SOCKS. You should have set the DynamicForward option in your [[#Configuration|~/.ssh/config file]] before.
###### Proxy SOCKS reuse
You don't have to create a [[Proxies#SOCKS5|Proxy SOCKS]] for each target SSH server you want to connect to.
Supposing you want to connect to two SSH servers: `A` and `B`, you can configure SSH activate  when you connect to `A` (by setting the DynamicForward option in your [[#Configuration|~/.ssh/config file]] before). Then, you can connect to `A` by [[#ssh (Connect remotely to the SSH server)|ssh]]. Once connected, the Proxy SOCKS would be active in your local machine, so you can instruct your Web Browser to use that Proxy SOCKS to connect to `B`.
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
- `-v`: for debugging (show verbose)
- `-vvv`: for more detailed debugging (show detailed verbose)
- ```-p <PORT>```: specify destination port
- ```-i <keyfile>```: specify the name of the [[#keyfile]].
- `-o`: to specify additional configurations. For example:
	- `-o KexAlgorithms`: to specify Key Exchange Algorithm. When you need to connect to some corporative SSH server, you might need to set this parameter, specially when you find some errors like `debug1: expecting SSH2_MSG_KEX_ECDH_REPLY` when showing verbose
- `-F </path/to/config>`: explicitly specify [[#Configuration|config]] file
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
- You may need to set `666` or `777` permissions on the filename or path you want to send to remote machine with [[GNU#chmod (Change file or directory permissions)|chmod]]
- To better debugging of a possible problem, use [[UNIX#sftp|sftp]]. You will be able to see the transferring data status. For example, the file might be _Stalled_
- You may need to limit bandwidth with `-l <bandwidth [B/s]>` (e.g. `-l 8192`)
- It might be a timeout issue: the remote server closes connection because does not receive client data after some elapsed time. To do so, use scp with `-o ServerAliveInterval=<time [s]>` (e.g. `<time [s]>=10`) to send a [[#KeepAlive]] message to server each `<time [s]>` seconds
### Common errors
#### ssh: Could not resolve hostname ...: Temporary failure in name resolution
Run [[#ssh (Connect remotely to the SSH server)|ssh command]] with detailed verbose: `-vvv`. It might not be taking the configuration file from `~/.ssh/config` file, but from `/etc/ssh/ssh_config` instead due to file owner or file permissions.
If it is not taking the configuration file from `~/.ssh/config`:
- [[GNU#chmod (Change file or directory permissions)|chmod]] `.ssh` directory with `700` permissions: `sudo chmod 700 ~/.ssh`
- [[GNU#chmod (Change file or directory permissions)|chmod]] all the inner files with `600` permissions: `sudo chmod 600 ~/.ssh/*`
- [[GNU#chown (change ownership)|chown]] all the inner files inside `.ssh` directory to your user: `sudo chown <user>: ~/.ssh/*`