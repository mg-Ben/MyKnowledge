---
tags:
  - Networks
---
# Core principles
Internet has two main definitions:

- Interconnection of **Hardware and Software** components ("Nuts and bolts") - It's like an objective approach
- Network infrastructure which provides **services to distributed applications** - It's like an subjective approach

The first one is the objective view of Internet. The second one is more subjective: what we can do with those components.
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
- A router
- A NAT
### Client-Server communication
- **Server**: a running process which is usually indefinitely awaiting for connections and which provides a service to the client (e.g. a webpage)
- **Client**: the process that wants to be served and sends requests to the server (e.g. our web browser-background process, when we are surfing the internet)
### Peer-to-Peer communication


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


