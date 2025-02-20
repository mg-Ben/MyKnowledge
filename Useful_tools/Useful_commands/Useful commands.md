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
# nslookup
_Available in [[Windows]] [[Windows#CMD|CMD]] and [[Windows#PowerShell|PowerShell]] by default_
To retrieve the [[IP#IP address|IP address]] for a given [[DNS|Domain name]]:
```shell
nslookup <domain.com>
```
# telnet
Connect your keyboard to a [[Internet#Socket]]:
```shell
telnet <targetIP>
```
We can log into a remote machine by telnet and get access to its [[Operating System#Shell]].
# curl
Make requests to [[HTTP|HTTP endpoints]]:
```shell
curl -X <HTTP_method> <HTTP_endpoint>
```
Flags and options:
- `--cacert <path_to_CA_cetificate>`: specify a [[Cybersecurity#Certificate Authorities (CA)|CA certificate file]] to trust in. You may need to run curl with [[Linux#Users#Super/Root users|sudo]] as the certificate file might be private
- `--insecure`: if you want to connect to a remote endpoint without specifying a CA certificate to trust in (insecure mode)
- `-s or --silent`: silent mode. Used to prevent the command output from displaying additional data like this:
```
 % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 10792    0 10792    0     0  96603      0 --:--:-- --:--:-- --:--:-- 97225
```
- `-H <field>:<value>`: set [[HTTP#Header]] values (e.g. `-H "Content-type:application/json"`)
- `-d`: whenever you perform a [[HTTP#POST]] operation, data must be specified. The `-d` flag allows user to specify which data to send (e.g. `-d @file.json`). You can even set the data manually, like `-d "{"name": "Josh", "age": 21}"`
# ack
Search inside all the plain-text file contents (such as logs, configuration files, text files, code files...) of some directory (you can search recursively, i.e. inside all the subdirectories) and find all ocurrences of some word or regular expression:
```shell
ack <regexp>
```
# arp
Perform a discovery to get all [[IP]] addresses from all devices in a [[Internet#LAN|LAN]]. However, the [[ARP]] table only contains, for each LAN portion to which each interface of our system belongs to, the devices we have already connected to, instead of other devices we have never met. Therefore, the first step is performing a ping to everyone in the same LAN setting as IP dest the [[IP#IP address range|broadcast address]].
1. `ping <broadcast_address>`
2. `arp -a`
# htop
An enhanced version of [[UNIX#top|top command]]. It is very similar to [[Windows#Task Manager]].
```shell
htop
```
# free
Prints current [[Operating System#Main memory (RAM)|RAM]] utilization on [[Linux]] [[Operating System]].
```shell
free
```
Interesting flags:
- `-h`: display information in a better-readable human format
- `-s N`: repeat printing every N seconds
# nc (netcat)
_It comes pre-installed in well-known [[Distributions]] such as [[Distributions#Ubuntu|Ubuntu]], [[Distributions#Debian|Debian]] or [[Distributions#CentOS]]_
```shell
nc <dest_IP> <dest_port>
```
You can use this command to test connectivity to [[SSH|SSH server]] or [[HTTP|HTTP server]], just by specifying the target port.
By default, the ``netcat`` command would send raw data as a [[TCP]] or [[UDP]] segment (e.g. to establish a connection), with no [[Layer architecture|Application Layer]] headers. That's why it works with any [[Layer architecture|Application Layer]] protocol; because the communication occurs up to [[Layer architecture|Transport Layer]].
However, you can craft an [[Layer architecture|Application Layer]] packet with `netcat` by the use of [[UNIX#(Pipes)]]. For example, to add [[HTTP]] headers:
```shell
echo "GET / HTTP/1.1\r\nHost: example.com\r\n\r\n" | nc example.com 80
```
# lshw
Get detailed Hardware information:
```shell
lshw
```
With this command, you can get the [[Operating System#CPU Architectures|CPU architecture]], for example.
Interesting flags:
- `-c <classname>`: to get specific information about some component (e.g. `processor`)
# tcpdump
A powerful command-line packet analyzer tool used to capture and display network traffic. It allows users to monitor and analyze packets that are transmitted or received over a network interface.
Analyze packets of various protocols, including [[TCP]], [[UDP]], [[ICMP]], and more.
```shell
tcpdump -i <interface>
```
# jq
Whenever you need to print [[JSON]] data by terminal, you might want to display data in a better human-readable way (e,g, with colours for each field). In these case, you can use `jq` command with [[UNIX#(Pipes)|pipes]]. The output of the previous command will be passed as an argument for `jq` like:
```shell
echo '{"example": 3}' | jq
```
You can also specify an input JSON file:
```shell
jq <file.json>
```
Interesting options:
- Show an specific field in JSON: `jq '.<fieldname>'`