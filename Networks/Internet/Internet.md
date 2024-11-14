---
tags:
  - Networks
---
# Core principles
Internet has two main definitions:

- Interconnection of **Hardware and Software** components ("Nuts and bolts") - It's like an objective approach
- Network infrastructure which provides **services to distributed applications** - It's like an subjective approach

The first one is the objective view of Internet. The second one is more subjective: what we can do with those components.
## Router
It is a packet switcher that receives packets through some input links and sends the received packets to some output links.
It is the same as [[#Switch|switches]], but they differ in the [[OSI Model|Internet layer]] they work. While switches operate in [[#Link Layer]], routers operate in [[#Network Layer]].
## Switch
It is the same as [[#Router|routers]], but they differ in the [[OSI Model|Internet layer]] they work. While routers operate in [[#Network Layer]], switches operate in [[#Link Layer]].
## ISP
_ISP stands for Internet Services Provider_
The [[#Router|routers]] and [[#Switch|switches]] that we all use on the Internet don't belong to the Internet users, but to **Internet Services Providers**. Summarized, the ISPs can be viewed as an interconnection of routers, and thanks to ISPs we have Internet connectivity from our homes.
There are different hierarchy levels:
- University ISPs
- Corporative ISPs
- Regional ISPs
- Global ISPs
_Examples: coffee shops Internet, hotel Internet or any public and free Internet_

![[ISPs.png]]
## Links
The way to interconnect two Internet devices (such as router-router, router-host, switch-router...).
### Coaxial cable
### Copper wire
### Optical fiber
### Radio spectrum
## LAN
_LAN stands for Local Area Network_
It is a concept that represents a network of interconnected Internet Devices in a local range (such as offices, universities, coffee shops...).
We can have:
- **WLAN**: Wireless LAN
- **VLAN**: Virtual LAN
- **LAN**: Traditional physical LAN
### LAN portion
A LAN may contain sub-LANs, called **LAN portions**.
Each (sub-)LAN has got:
- Its own [[IP#IP address range|IP address range]]
- One [[IP#IP address range|broadcast address]]. When we communicate with it, that packet will be sent to all devices attached to that LAN portion
Each [[IP#Network Interfaces|Network interface]] in your machine has got its own LAN portion. For example:
- The LAN (WLAN) to which your computer antenna is attached to
- The LAN to which your computer Ethernet cable is attached to
- The [[Virtualization#Hyper-V LAN]] that allows you to connect to a [[Virtualization#Bridge mode|bridged Virtual Machine]]
![[interfaces-lans.drawio.png]]
## WAN
_WAN stands for Wide Area Network_
It is a concept that represents a network of interconnected Internet Devices in a wide range.
## API
_API stands for Application Programming Interface_
When we understand Internet like [[#Core principles|a infrastructure that provides services to distributed applications]], we must consider the concept of **API**. 
API is a set of rules, protocols, and tools that allows different software applications to communicate with each other. APIs define the methods and data formats that developers can use to interact with a service or platform, abstracting away the underlying implementation details.
# The Internet boundary
## Terminal systems
Internet can be depicted as a huge network of interconnected devices. In the Internet slang, we call **Terminal Systems** (also known as **Hosts**) to the end to end communication between two parts, because that communication takes place on the boundaries of Internet.
![[Internet-Terminals-Concept.png]]
Those terminals might be:
- User's machines
- Servers
- Network balancers, firewalls, switches, gateways...
And they can communicate each other in different ways.
### Gateways
A gateway machine actually means:
- A [[Proxies|Proxy]]
- A [[Firewalls|Firewall]]
- A [[Balancers]]
- A [[#Router]]: for example, your home router is a Gateway
- A [[NAT]]
### Client-Server communication
#### Server
A running process which is usually indefinitely awaiting for connections and which provides a service to the client (e.g. a webpage).
#### Client
The process that wants to be served and sends requests to the server (e.g. our web browser-background process, when we are surfing the internet).
- The client is the communication side that starts the communication.
### Peer-to-Peer communication
Both the [[#Client]] and [[#Server]] awaits for connections or starts the communication. Therefore, there are not dedicated servers.
For example:
- bitTorrent
- OS updates
- Skype
# Transport Layer
## Port
A single [[IP#IP address]] machine's interface can run several processes, each one with an assigned **port number**.
## Socket

# Network Layer
## Virtual Circuit
A Virtual Circuit is a way to route the network-layer traffic between origin and destination via dedicated-path configured before the beginning of the communication.
# Hands on
## Interact with running ports
You can interact with running ports on some [[IP#IP address|IP Address]], including your own machine (```localhost```) by:
- A web browser
- [[Useful commands#curl|curl command]]
# IANA
_Internet Assigned Numbers Authority_
# VPN
_VPN stands for Virtual Private Network_
It can be considered as a type of [[#Virtual Circuit]], but not exactly the same.
It is a way to establish a connection inside a private and protected network.
## VPN Server
Provides the VPN functionality by allowing the user to browse securely and change their [[IP#IP address|IP address]].

# Web server
A [[#Server]] that delivers [[#Web content|web content]] to [[#Client]] (i.e. users) by processing [[#Web server#Supported protocols|HTTP requests]].
## Web content
A Web server offers static content.
It can be:
- [[HTML]] files
- Images
- [[CSS]] stylesheets
- [[JavaScript]] files...
Web servers can also handle [[#Application content|dynamic content like Application Servers]], but they typically do this by interacting with application servers or scripting engines (like [[PHP]] or [[Python]]) to generate that content.
## URL
### Endpoint
Examples: `https://myserver1.com/users`, `https://myserver1.com/accounts`, `https://myserver1.com/taxes`...
### Query string
## Supported protocols
Typically [[HTTP]] and [[HTTP#HTTPS|HTTPS]].
## Examples
### AHS
_AHS stands for Apache [[HTTP]] [[#Server]]_
_Also known as **Apache**, **Apache Web Server**_
It is an Open-source [[#Web server]] developed by **Apache Software Foundation**. It serves static [[#Web content]]. However, can handle dynamic content through integrations with [[Python]], for example.
### IHS
_IHS stands for IBM HTTP Server_
It is a the implementation of [[#AHS]], but developed by IBM.
### NGINX
### [[Windows#IIS]]
# Application Server
A [[#Server]] that delivers [[#Application content]] to [[#Client]] (i.e. users) by processing **business logic** and [[#Application Server#Supported protocols|a variety of different protocols]].
## Application content
An Application Server offers dynamic content.
This means that what it serves is **running code**, such as [[Java#Servlet#Hello world Servlet Example|Java servlets]]. In other words, an Application Server provides an **environment** to run applications.
For example, an Application Server is able to pull data from a database and then render a dynamic webpage.
## Supported protocols
Not limited to [[HTTP]]. For example, can support other protocols such as [[SOAP]].
## Examples
### IBM WebSphere Application Server
_Also known as **WAS**_
Provides a [[Java#Java EE|Jakarta (Java Enterprise Edition)]] environment.
# Web Application Server
The same as [[#Application Server]], but focused on web-based applications. They differ in the [[#Web Application Server#Supported protocols|supported protocols]]; whereas an Application Server is not limited to HTTP, a Web Application Server is, as it is web-based.
## Supported protocols
Typically [[HTTP]] and [[HTTP#HTTPS|HTTPS]].
## Examples
### Apache Tomcat
It is a [[Java#Servlet#Container|Java Servlet Container]].
It is a [[Java]] [[#Application Server]]. Developed by **Apache Software Foundation**.
