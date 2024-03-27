---
tags:
  - Networks
  - Protocols
---
# Core principles
_SSL stands for Secure Sockets Layer_
_TLS stands for Transport Layer Security_
They are very similar: TLS is the evolution of SSL, though. They are **protocols**.
In the [[OSI Model]], SSL/TLS belongs to the **Transport Layer**. However, it can operate in conjunction with another **Transport Layer protocol**, such as [[TCP]] or [[UDP]]:

![[SSL-TLS_OSI_Layer.png]]

They provide [[Cybersecurity#Authentication]] and [[Cybersecurity#Confidentiality]] through [[Cybersecurity#Encryption#Asymmetric encryption]] public-private key pair. However, in this case, we use **certificates**, which are analogous to those public-private key pairs.

- The [[HTTP]] + SSL/TLS combo is also called [[HTTP#HTTPS]].

# Implementations
## OpenSSL
It is the open-source implementation as a toolkit (basically, a software library) to provide SSL/TLS services.
## elasticsearch-certutil
The tool for managing certificates in a Elastic Stack.