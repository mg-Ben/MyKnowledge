---
tags:
  - Networks
  - Internet
  - Devices
---
# Core principles
A Proxy is a process or [[Internet#Terminal systems|Host (server)]] which acts as an intermediate between a [[Internet#Client-Server communication|Client and a Server]]. It provides:
- **Security and Privacy**
    - **Anonymity**: Proxies can hide the client’s IP address from the target server. The target server only sees the IP address of the proxy server, which can help protect user privacy
    - **Content Filtering**: Proxies can block access to specific websites or content based on predefined rules, making them useful for implementing organizational policies or parental controls
    - **Threat Protection**: Some proxies offer features like malware scanning and blocking malicious content
- **Performance and Caching**
    - **Caching**: Proxies can cache frequently requested resources. When a cached resource is requested again, the proxy can serve it directly, reducing the load on the target server and improving response times for clients
    - **Load Balancing**: Proxies can distribute incoming requests across multiple servers to balance the load and improve performance and reliability
- **Access Control**
    - **Network Access**: Proxies can enforce network policies by allowing or denying access to specific resources based on IP addresses, domain names, or other criteria
    - **Authentication**: Proxies can require users to authenticate before granting access, which adds a layer of security
# Examples
## SOCKS
_SOCKS stands for SOCKet Secure_
Handles any type of traffic, including TCP and UDP, and is not limited to web traffic. SOCKS proxies operate at a lower level and can support a wide range of applications.
## HTTP Proxy
## HTTPS Proxy

## Reverse Proxy
