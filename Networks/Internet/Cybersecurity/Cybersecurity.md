---
tags:
  - Networks
  - Internet
---
# Core principles
The three main parts of cybersecurity are:
- **Availability**
- **Integrity**
- **Confidentiality**
_Known as CIA triad_

![[CIA-triad.png]]

But we add in **Authentication**, because it is another important part of cybersecurity that is not covered by CIA triad. Not every points of cybersecurity are addressed by CIA triad. The extended CIA triad covers more issues about cybersecurity:

![[Extended-CIA-triad.png]]
## Availability
All network resources must be available for all users.
## Authentication
### Introduction

![[spiderman-authentication-meme.png]]

The definition of authentication is:
- The process of verification that `user/machine is == who they claim to be`
In every authentication process, there are two parts:
1. [[#Registration process]]: the other side of the communication introduces itself to us
2. [[#Login process]]: the other side of the communication wants to get access to some service. This process is also called **Authorization**
#### Registration process

![[RegistrationProcess.drawio.png]]

In a registration process, the user or machine introduces itself. However, we can't know who the user or machine really is. For example, Bob might create a fake account and the server would never know it.
Nonetheless, we can:
- Verify that the user or machine is providing information that makes sense (i.e. a **coherence** detection may be performed in this step). However, most systems don't implement this coherence detection
- Verify that the user or machine identity information does not really exist against a **database**
In practice, in most cases we will accept anyone in the system.

Now, we have to consider that the provided data in **registration** step will be used later for [[#Authorization]] stage, in the **log in** process. We don't want others to be able to get access to the system, so the provided information must be **(public)-unique** and **private-(non-unique)**. Sometimes those unique-private properties are separate, but in the whole the combination of final provided data must meet those two properties:
- (Public)-unique because we are talking about the identity of the self (that is of what statement is about), and we must check whether that user already exists because there can't be two identic users. This value can be public or not, because it is used just for indexing in database. 
    _An example are usernames, which are unique for each user (remember that sometimes, when you register with an existing username, you are given an error message that says "Username already exists"), or phone number, or mail addresses_
- Private-(non-unique) because the main purpose is to prevent hackers from impersonating someone by making them more difficult to lie. With privacy, we can prevent hackers from providing the credentials of other person.
    _An example are passwords, which are private for each user, but can be non-unique (several users might have the same password)_
In the whole, _(user, password)_ combination is both unique and private: those are the main conditions to meet.
An example of information that meets both unique-private at a time are fingerprints. You can use fingerprint as index, as it is unique, and fingerprint is difficult to steal (thanks to privacy).

Examples: in the following case, we have username and password. The tuple meets both uniqueness and privacy:

| username: unique | password: private | (username, password): unique and private |
| :--------------: | :---------------: | ---------------------------------------- |
|       john       |        ***        | (john, ***)                              |
|       paul       |        **         | (paul, **)                               |
|      george      |     ********      | (george, ****)                           |
|      ringo       |    **********     | (ringo, **)                              |

But in this case:

| username: unique | phone number: unique | (username, phone number): unique |
| :--: | :--: | ---- |
| john | 12 34 56 | (john, 12 34 56) |
| paul | 46 32 22 | (paul, 46 32 22) |
| george | 82 73 73 | (george, 82 73 73) |
| ringo | 31 99 56 | (ringo, 31 99 56) |

Although data is unique, anyone can impersonate john, paul, george or ringo, because data is not private.

In this other case:

| password: private |
| :--: |
| *** |
| ** |
| ******** |
| ********** |

As data is non-unique (there might be duplicate passwords!), the server cannot check if a new user already exists. Therefore, register process is not performed and database makes no sense.

At the end, the resulting combination properties can be interpreted as an ```OR``` operation. If we define:
- Non-unique: ```0```, Unique: ```1```
- Public: ```0```, Private: ```1```
Then, if we have a data that is, for example, non-unique and private (```01```) and another that is Unique and private (```11```), the resulting combination is (```01 + 11 = 11```), i.e. unique and private. What is the same; to get a ```11``` unique and private value combination, some data must be unique and some data (not necessary the same) must be private.

Other examples are: voice features or face features, which are both unique and private. You can make an authentication system based only on fingerprints, for example, but remember the more data you ask for the user the more accurate the liar-detector is.
Now, maybe you have noted that, to introduce yourself on the Internet, you have to provide something that is **private**. Unless you encrypt it, anyone can steal it! That's why authentication is something that implicitly must go-in-hand with [[#Confidentiality]].
#### Authorization - Login process
Once a user/machine has been authenticated, it is authorized.
Authorization involves, summarizing, providing mechanisms to grant certain permissions to certain users.
To do so, we must create a database of **authorized users** or directly use [[#Certificates]].
Certificates are a way to allow users to access to the system without the need of prior [[#Registration process]]. However, the provided certificate must be signed by some reliable [[#Certificate Authorities (CA)|Certificate Authority]]. That's why the receiver must have a list of trusted **Certificate Authorities**.

![[LogInProcess.drawio.png]]
##### Can I use my private key to authenticate myself?
_Theoretically you could, but not recommended_
Your private key is something that meets the [[#Authentication information properties|two requirements]] needed for determining the identity of someone: unique and private. Therefore, it is something you can provide to an authentication system to show your own identity.
Now, you have to encrypt your private key. You encrypt it with destination's public key and destination decrypts it with their private key. Nevertheless, the destination would have your private key, but you may wonder: what's the problem of having a private key instead of having biometric private data of the user? The response is the utility:
- If destination has got your biometric data and It turns out to be fake server or the server data is stolen by a cyberattack, they can impersonate you, but, at least, that's the only thing they can do. That threatens only your Authentication
- If destination has got your private key, they can both: impersonate you and decrypt everything that goes towards you. That threatens both: your Authentication and your Confidentiality
For this reason, It is not recommended.
##### Can I use my public-private key pair to authenticate myself?
_Short answer: Yes._
- Private key is private
- Public key is unique
However:
The problem is the same as [[#Can I use my private key to authenticate myself?]] You would have to [[#Authentication information properties|register]] in server and send your public-private key pair. The server would store your public-**private** data in database. Therefore, the server would have your private key and that threatens your Authentication and Confidentiality.
In this particular case, there isn't database, just log in process. The coherence-detection involves using the user's public key to decrypt a sample message which has been encrypted with the private key of the user.
To prevent malicious users from accessing the service, we must grant [[#Authorization]] here.
## Integrity
It means that your data cannot be manipulated by someone during the way. On Internet, hackers can manipulate your packet to change the destination, for example. **However, integrity cannot prevent hackers from manipulating the bit order of the packets you send**.
integrity can be addressed by [[#Hashing]] and [[#Encryption]].
## Confidentiality
It involves preventing hackers from accessing the network information that you send to others, but also from accessing to some services (also known as [[#Authorization]]). This is, you can provide **privacy**. However, **It doesn't prevent hackers from sending you information.**
Confidentiality can be addressed by using [[#Encryption]].
## Encryption
### Asymmetric encryption
It consists on [[#Public key]]-[[#Private key]] pair.
When you send information to some destination, your data must be encrypted using the destination's [[#Public key]]. Then, destination can decrypt it using their [[#Private key]]. If you don't have destination's [[#Public key]], you cannot encrypt your data to talk with them, so **be careful**. Your security depends on the other's decision. Only talk with those who have public keys (e.g. [[HTTP#HTTPS|HTTPS]] servers).
_It's as if a human wants to talk to another one in a clandestine way, so that no one else an understand what they are saying. It's logic that there must be an arrangement between both parts. If one doesn't want to, confidential communication cannot take place. If you have a public-private key pair, It's for other's security, not for your own security :(_
When you receive information from destination, it must be encrypted too, but using your [[#Public key]]. Thus, you can decrypt it with your [[#Private key]].
#### Public key
The public key, as it name suggests, is sharable to other users. You only must share it to the other part of the communication, which is a trusted host. However, it can be filtered and anyone might have it. The main purpose of this key is to tell others how they must encrypt their data so that you can decrypt it.
It also serves for authentication: you provide to the destination your public key so that they can trust you. Then, you encrypt a message with your private key and the destination will decrypt the message with your public key. If the message makes sense, the destination confirms that you are you.
_The public key is the data you use to perform some operation over the data you transmit/receive. That operation is always the inverse of the corresponding private key._

#### Private key
The private one is **not** sharable, and you must keep secure on your system.
Whenever you want to share confidential information with a destination, you must have some agreement with them. That agreement involves information that only you and the other part of the communication know. With that information, you can turn a message into a non-legible message for anyone, just by applying that information to the message (i.e. encrypt the message).
However, the same information must be used in destination to turn the message back to a legible format (i.e. decrypt the message). Therefore, a **transformation** _f(x)_ must be applied to a message _x_, and there must exist an **inverse transformation** _f<sup> -1</sup>(x)_ too that must be applied to the encrypted message _f(x)_ such that _f<sup> -1</sup>{ f(x) } = x_. That transformation and the inverse transformation must use some **information**, called **key**. The transformation box is something that everyone knows how it works. The encryption algorithm cannot be confidential since, if we want to make it confidential, we should encrypt it and then it would be a paradox. Thus, both the transformation and the inverse transformation are known by everyone on the Internet.
##### What would happen if key didn't exist?
Imagine that the applied transformations didn't use any additional information. For example, the encoding transformation is _f(x) = 3x_. Therefore, the inverse one is _f<sup> -1</sup>(x) = x/3_, as _f<sup> -1</sup>{ f(x) } = f<sup> -1</sup>{ 3x } = 3x/3 = x_. If Bob sends _x = 5_, the encoded message is _15_. As the transformation is something that everyone knows, a hacker could recover the original message just by calculating _f<sup> -1</sup>(x) = 15/3 = 5_.
Therefore, the encrypted message must be something such that, applying the inverse function _f<sup> -1</sup>(x)_ doesn't let the user know the exact value of the original message _x_. For example, a function that implies information loss might be suitable in this case: _f(x) = round(x)_. When the message is _x = 3.4_, the encoded message is _x = 3_. A hacker cannot know which was the real value from _x = 3_ (_x = 3.1? Or x = 3.4? Or maybe x = 3.3?_).
But now, the destination must be able to decrypt it, and in this case they can't. Therefore, the second condition is that the function uses some additional parameter that must be only known by origin and destination.
For example: _f(x, n) = n*x_. If Bob encrypts the message using _n = 45_ for example, a hacker couldn't decrypt it as they don't know _n_, but the destination could if they know _n_. In this example, _n_ is the key information. This is the case of symmetric keys, as _n_ is a shared information secret which is the same in destination and origin. This implies security risks, as the destination might betray you sharing the value of _n_ to others. By this reason, asymmetric keys are recommended.
Asymmetric keys imply using different values for encryption and decryption: _k<sup>+</sup>_ and _k<sup>-</sup>_, respectively (remember that we encrypt the message with the destination's public key). The public key is _k<sup>+</sup>_ and the private is _k<sup>-</sup>_. In this case, we must find an encrypting function _f(x, k<sup>+</sup>)_ and its inverse function _f<sup> -1</sup>(x, k<sup>-</sup>)_ such that _f<sup> -1</sup>{ f(x, k<sup>+</sup>), k<sup>-</sup> } = x_. The value of _k<sup>+</sup>_, the public key, cannot determine the value of _k<sup>-</sup>_, as the purpose is to prevent anyone from knowing the private key. Ideally, _k<sup>-</sup>_ shouldn't be related to _k<sup>+</sup>_. Unfortunately, it is, because of the condition: _f<sup> -1</sup>{ f(x, k<sup>+</sup>), k<sup>-</sup> } = x_.
- However, the encryption function _f(x, k<sup>+</sup>)_ shouldn't be invertible because, otherwise, anyone could decrypt it analytically.
- But, if _f(x, k<sup>+</sup>)_ is not invertible, the recipient couldn't decrypt it.
The response is: the function _f(x, k<sup>+</sup>)_ must be hardly-invertible, in the sense that it must be invertible, but the expression of the inverse function must be such that it depends on some non-explicit parameters. For example, the function _f(x)_ needs to depend on some product _n = pq_, but the inverse one must depend somehow on _p_ or _q_ independently. Thus, the function is indeed invertible for everyone, but you would need to find the exact values of _p_ or _q_ to invert it. A hacker could find the _(p, q)_ combination, but it would take a lot of time to find them. We can denote as:
- _f(x, (p, q, r, s, t...))_ the encryption function, which depends on the _(p, q, r, s, t...)_ cluster combination, which is public
- _f<sup> -1</sup>{ f(x, (p, q, r, s, t...)), p, q, r, s, t...}_ the decryption function, which depends on the _p, q, r, s, t..._ parameters independently so it's very difficult to find each one from the combination
Note that the essence is in designing a function that is invertible, but very hard to invert for someone who doesn't know each separate parameter. The complexity is in finding the function that indeed depends on those parameters. For example, you cannot define a function such as:
    _f(x, (p, q, r, s, t...)) = p*q*r*s*t * x_
Because the reverse function does not depend on the separate parameters, but in the cluster:
    _f<sup> -1</sup>{ f(x, (p, q, r, s, t...)), p, q, r, s, t...} = f(x, (p, q, r, s, t...)) / (p*q*r*s*t)_
So a hacker could easily decrypt the message, as _pqrst_ is public information.
The idea is that the public key must be a clustered information, that consists on several unitary parts, and the private key are the unitary parts themselves. The encryption function uses the clustered information, and the inverse one uses each unitary part. Now you can see that not every function is valid: only those whose inverse depends on each unitary part, and that's not easy to find.
Finding a function which meets these requirements can be non-trivial, but fortunately there is some useful example:
_f(x, (p, q)) = x<sup>e</sup> mod (pq)_ such that _e != p, e != q and e < pq, and must not have common factors with (p-1)(q-1)_
_f<sup>-1</sup>{ f(x, (p, q)), p, q} = f(x, (p, q))<sup>{ [(p-1)(q-1)Z + 1]/e }</sup> mod (pq)_
The value of Z is chosen such that the term _{ [(p-1)(q-1)Z + 1]/e }_ is an integer value.
As the recipient of the message doesn't have the value pair _p, q_, they cannot decrypt easily the message without that information (they could, but it would be very difficult).

_The private key is the data you use to perform some operation over the data you transmit/receive. That operation is always the inverse of the corresponding public key._
_The private key is a value that is used as an input in a algorithm whose output is the public key._
#### What happens if a hacker gets your public key?
Then the hacker will be able to send you messages, but it is not a serious security risk.
#### What would happen if you used your private key to encrypt your data?
Then, anyone with your [[#Public key]] (i.e. anyone on the Internet) might access to the information you send, such as credit card, passwords or whatever, so don't do it.
#### Examples
##### GPG Keys
Refer to [[GNU#GPG public and private keys]].
##### SSH Keys
Refer to [[SSH#SSH public and private keys]].
## Hashing
Involves creating a single number that is an operation result applied to the data you send. Therefore, that number should be unique for the binary data you send around Internet. In this way, if a hacker manipulates your network data, the number no longer corresponds to the manipulated data and the recipient of the information would realize.
## Certificates
Whenever we want to provide [[#Authorization - Login process|Authorization]], certificates come into play. Certificates are a way to turn someone into a trusted user or machine.
In practice, certificates can be for example:
- `.p12` files
- `.pem` files
- `.key <-> .crt` pair files. Both represents 1 certificate
### Certificate Authorities (CA)
The entities that signs certificates. That entity might be an organization or yourself.
A certificate authority has got its own certificate.
- **For example**: If some server uses [[HTTP#HTTPS|HTTPS]], they present a [[SSL-TLS]] certificate to client (us) so that the client (us) can trust the server. However, the certificate might be fake, so we need the client to trust the CA.

The client side has got a list of trusted CAs.
- **For example**, in [[Internet#Interact with running ports|web browsers]], you can go to Settings > Certificates and see the certificates trusted by the web browser:
	![[trustedCAs.png]]
- In [[Internet#Interact with running ports|curl command]], the `--cacert` flag makes the client (us) trust some CA by specifying its certificate file.
## Example algorithms
### RSA
_Rivest-Shamir-Adleman_
Provides 
### AES
# Ethical hacking
# Penetration testing
## Enumeration
It involves studying the target victim machine and documenting it.
To do so, follow these steps:
1. Discover the ports currently running on remote machine through a [[Useful commands#nmap]] command.
2. Identify the [[Telnet]] or [[SSH]] service on the remote machine and try to connect to it to get access by terminal.
# Digital Forensics
# Reverse Engineering
# Forensic Analysis
# Incident Response
# Common attacks
## Brute Force
It involves creating a Script to try with lots of username/password credentials (or other [[#Authentication]]) per second against an authentication server.
