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
_Reverse of [[#RemoteForward]]_
_For further information: [A Visual Guide to SSH Tunnels: Local and Remote Port Forwarding (iximiuz.com)](https://iximiuz.com/en/posts/ssh-tunnels/)_
The `LocalForward` option generically allows us to forward every request made from an external device to the _Our host_ (through some of our [[IP#Network Interfaces|network interfaces]]) to some _Target host_ inside the remote private network. For example:

![[LocalForwardIdea.png]]

The generic syntax is:
```
RemoteForward [local_listening_interfaceIP:]local_port remote_address:remote_port
```
For the example above:
```
RemoteForward 9.8.7.6:8080 123.234.1.1:9119
```
Where:
- `[local_listening_interfaceIP:]` is the [[IP#IP address|IP address]] of the interface inside our host where we will be listening for requests. In the example above, it is the WiFi Adapter Network Interface IP Address
- `local_port`: the [[Internet#Port|port number]] where we are listening for requests
- `remote_address`: the IP address where the traffic will be forwarded to (_Target host_). This IP address is the IP address **seen from the remote machine**, where the SSH server is running
- `remote_port`: the port inside the _Target host_ where the forwarded traffic will be sent to

Note that:
- `[local_listening_interfaceIP:]` can be the [[IP#Network Interfaces#Special IP addresses|localhost (127.0.0.1) interface]]
- `remote_address` can be `localhost`

This is:
```
LocalForward localhost:8080 localhost:9119
```
With this setup, you would have something like this:
![[LocalForwardIdeaLocal.drawio.png]]

##### RemoteForward
_Reverse of [[#LocalForward]]_
_For further information: [A Visual Guide to SSH Tunnels: Local and Remote Port Forwarding (iximiuz.com)](https://iximiuz.com/en/posts/ssh-tunnels/)_
The `RemoteForward` option generically allows us to forward every request made from an external device to the _Remote host_ (through some of its [[IP#Network Interfaces|network interfaces]]) to some _Target host_ inside our private network. For example:

![[RemoteForwardIdea.png]]

The generic syntax is:
```
RemoteForward [remote_listening_interfaceIP:]remote_port local_address:local_port
```
For the example above:
```
RemoteForward 9.8.7.6:9119 123.234.1.1:8910
```
Where:
- `[remote_listening_interfaceIP:]` is the [[IP#IP address|IP address]] of the interface inside the remote host where the remote will be listening for requests. In the example above, it is the WiFi Adapter Network Interface IP Address
- `remote_port`: the target [[Internet#Port|port number]] where the traffic is sent from the external devices
- `local_address`: the IP address where the traffic will be forwarded to (_Target host_). This IP address is the IP address **seen from your local machine**, where you configure SSH
- `local_port`: the port inside the _Target host_ where the forwarded traffic will be sent to

Note that:
- `[remote_listening_interfaceIP:]` can be the [[IP#Network Interfaces#Special IP addresses|localhost (127.0.0.1) interface]]
- `local_address` can be `localhost`

This is:
```
RemoteForward localhost:9119 localhost:8910
```
With this setup, you would have something like this:

![[RemoteForwardIdeaLocal.png]]

In this case, you can make `localhost:8910` respond the request, for example.
Sometimes, the _Remote host_ does not have Internet reachability. In this case, you can configure RemoteForward to redirect the traffic towards your machine and then to an external [[DNS]] domain that will send back a response to the remote server (this technique is often refered to as **Proxychains**).
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
You can run a command in remote with:
```shell
ssh <destination> <command>
```
The value of ```<destination>``` can be:
- The destination [[IP#IP address|IP address]] or [[DNS]]
- The username + the destination [[IP#IP address|IP address]] or [[DNS|domain name]], separated by ```@``` (e.g. ```bmartin@1.2.3.4```)
Flags:
- `-v`: for debugging (show verbose)
- `-vvv`: for more detailed debugging (show detailed verbose)
- ```-p <PORT>```: specify destination port
- ```-i <keyfile>```: specify the name of the [[#SSH public and private keys|private key]] file.
- `-o`: to specify additional configurations. For example:
	- `-o KexAlgorithms`: to specify Key Exchange Algorithm. When you need to connect to some corporative SSH server, you might need to set this parameter, specially when you find some errors like `debug1: expecting SSH2_MSG_KEX_ECDH_REPLY` when showing verbose
	- `-o ConnectTimeout=<time [s]>`: specifies the maximum amount of time, in seconds, that the SSH client should wait while trying to establish a connection to a remote host
- `-F </path/to/config>`: explicitly specify [[#Configuration|config]] file
- `-T`: prevent the session from creating an interactive terminal. This is useful when you only need to run a single command in remote and then close the connection automatically, like `ssh -T <destination> echo "hello"`
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