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

They provide [[Cybersecurity#Authentication]] and [[Cybersecurity#Confidentiality]] through [[Cybersecurity#Encryption#Asymmetric encryption]] public-private key pair. However, in this case, we use [[Cybersecurity#Certificates]], which are analogous to those public-private key pairs.

- The [[HTTP]] + SSL/TLS combo is called [[HTTP#HTTPS|HTTPS]].

# Implementations
## OpenSSL
It is the open-source implementation as a toolkit (basically, a software library) to provide SSL/TLS services.
### Convert from .p12 to .key + .crt
```shell
openssl pkcs12 -in <certificate.p12> -out <output.crt> -clcerts -nokeys
openssl pkcs12 -in <certificate.p12> -out <output.key> -nocerts -nodes
```
### Convert from .p12 to .pem
```shell
openssl pkcs12 -in <certificate.p12> -out <output.pem> -nodes
```
From `.pem` file, you can obtain the [[DNS]] names configured for an [[HTTP]] certificate (e.g. [[ElasticSearch#Generate the Application-Layer certificate for the node|http.p12]] 
file in [[ElasticSearch]]). To do so, see [[#Get DNS names from .pem]].
### Convert from .key to .pkcs8.key
```shell
openssl pkcs8 -inform PEM -in <certificate.key> -topk8 -nocrypt -outform PEM -out <output.pkcs8.key>
```
### Get Fingerprint from .crt
```shell
openssl x509 -noout -fingerprint -sha256 -in <certificate.crt>
```
### Get DNS names from .pem
```shell
openssl x509 -in <http-certificate.pem> -noout -text | grep DNS:
```
## elasticsearch-certutil
The tool for managing certificates in a Elastic Stack.