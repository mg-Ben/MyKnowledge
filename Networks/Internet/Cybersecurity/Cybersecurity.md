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

The authentication is a complex and philosophical definition about the reality. The definition of authentication is **double**; either one or the other are valid:
- _The process of verification that someone **we don't know before** is who they claim to be_
- _The process of verification that someone **we know before** is who they claim to be_
It's important to take into account both of them, because they are opposed as we will see [[#Philosophical definition|later]]. In both cases, we have to detect a lie about the identity of the person and determine if they are saying the truth about themselves.
_Spoiler:_
- **Bad news**: you will never trust anyone on the Internet, as you can **never** know for 100% certain who the user behind the computer really is
- **Good news**: at least you can make more difficult to the user to lie by forcing them to send you as much information about their identity as you can
The authentication can be solved by the use of [[#Encryption]]. Summarizing, with any data that can show the identity of the user.
### Philosophical definition
In our life, there are statements we can determine with 100% of veracity score, but some others not. The statements are conditions under test (e.g. whether the sky is blue: ```sky == blue```) and, to check their veracity score, a coherent-detection algorithm must be performed. For instance, if someone tells you that they saw yesterday a blue dog, the only way to believe it is seeing it by your own, but that's impossible. Well, the truth is that even if you see it, it can be false, as the dog might have dyed hair, or maybe the dog is not a dog... (Here there would be a more-depth philosophical discussion about reality, but that's another thing). The truth is that only in mathematics field the statements can be 100% true, but not in reality: what you see, smell, feel, touch... Might not be real with 100% certain in a rigorous sense.

_So every statement that you are given must be under test because it might be a lie, and the coherent-detection must be performed so as to determine the veracity score and decide if you trust or not._
#### Coherence-detection analysis
It serves for determining the veracity score for a given statement by computing the inter-correlation between each parameter value.
![[Coherence-detection.drawio.png]]
_It is like a mentalist-liar-detector that asks things to liars_
##### Information quality
Now, we will define:
```ìnformation quality = number of data*information distance to the lie issue*privacy of data```
This value determines if the coherence-detection system is accurate or not with its output: the veracity score. The more information quality, the more accurate the veracity score is (e.g. a coherence detector with a low information quality is not reliable and if the output veracity is equal to ```0.75```, that measure is not reliable).
- The more variables under test, the more difficult for the liar to lie, because they have to create a "coherent story" with all those variables
- And if those variables are closer to statement, the information quality is better and more difficult for the liar to lie
- And if those variables are non accessible for the liar, it will be more difficult for the liar to lie, because they have to manage how to get access
To make a very accurate coherence detector (i.e. a detector that is very accurate with veracity measure, i.e. a good lie-detector), you now know that you must obtain as much information as you can and not only that, but also information the most related to the statement under test as possible, and private.
The coherence-detector is like a mentalist who ask questions to liars to determine whether they say the truth or not. A good mentalist would ask a lot of accurate questions. Liars try to create new realities from our existing reality (i.e. liars play God), but we have to check whether their reality is coherent with our one, so we have to assume that there only exists one reality: our one.
_Well, note that coherence-detection systems are biased by our reality, because they detect the coherence between data according to our reality laws, but... There might be other realities with their own laws which are also coherent as this world? That's another discussion that we are not considering here :D_
##### Example
Someone may lie you saying that "They work for Google company". Here, the statement under test is that ```job == Google```.
You run your inner coherence-detection system. Therefore, you ask as many questions as you can, related to the job and as private as possible, such as:
- Where they live
- The wage. As this is private information, if they were a liar, they wouldn't know what is the average wage to say
- The job card ID. As this is private information, if they were a liar, they wouldn't know the ID number
The more conditions you assess (the more data you get), the more rigorous you are to trust someone.
```
# Check if the person works for Google = Check all of these sub-conditions:
if(works_in_country_with_google_offices(suspicious_person.country) == True && has_good_wage(suspicious_person.wage) == True && ...){
	I believe them
} else{
	I don't believe them
}
```
##### Authentication information properties
When we are talking about authentication, there are two statements under test:
- _The process of verification that someone **we don't know before** is who they claim to be
- _The process of verification that someone **we know before** is who they claim to be_
This means: authentication involves checking either ```a new person == who they claim to be``` or ```a known person == who they claim to be```. As they are different statements under test with different coherence-detection procedure, we have considered separately. Although authentication is both definitions, in practice both definitions are separate conditions under test (i.e. separate lies we have to verify) that come from the user.
_Note that the user explicitly says "Hello, I want to register" when wants to get registered or "Hello, I'm ..." when wants to log in, so there are two different statements to check. The authentication cannot be defined as the process of verification that someone is who claims to be, as the user doesn't send only his information, but needs to explicitly say whether he is new or not for us, and consequently we have to check two separate conditions_
###### Registration process

![[RegistrationProcess.drawio.png]]

The first case involves checking ```a new person == who they claim to be```. To make a human simile, It's as if someone gets closer to you and says ```Hi, I introduce myself: mi name is Bob```. In this statement, the suspicious Bob claims two sub-statements:
- That we don't know him
- That he is Bob
To check the first condition, we need to take as much information as we can from the user, as related as possible to the issue under test (the identity of the user) and as private as possible. The number of data is important in this step because we will use all that information compacted (called biometric template) as an index to the database.
However, for the second one, **there is no way to check that condition for a server**.
Well, that's what happens in the **registration process** of a new user in a server, for example. The user introduces itself providing as much information as it is forced to give. Server designers decide to request all that information to make the authentication system more robust (remember [[#Information quality]]). For example:
- The fingerprint
- The face features
- The password or any private key
- The username
- The ID Card...
Here, the provided information must be **(public)-unique** and **private-(non-unique)**. Sometimes those unique-private properties are separate, but in the whole the combination of final provided data must meet those two properties:
- (Public)-unique because we are talking about the identity of the self (that is of what statement is about), and we must check whether that user already exists because there can't be two identic users. This value can be public or not, because it is used just for indexing in database. 
    _An example are usernames, which are unique for each user (remember that sometimes, when you register with an existing username, you are given an error message that says "Username already exists"), or phone number_
- Private-(non-unique) because the main purpose is to prevent hackers from impersonating someone by making them more difficult to lie, as we said in [[#Information quality]]. With privacy, we can prevent hackers from providing the credentials of other person.
    _An example are passwords, which are private for each user, but can be non-unique (several users might have the same password)_
In the whole, _(user, password)_ combination is both unique and private: those are the main conditions to meet.
To make the system even more robust, you can force the user to provide unique-private data. For example, you can use fingerprint as index. This would be more safe in the new user check, as fingerprint is difficult to steal (thanks to privacy). You can also add as many unique data as you can (username, phone numbers and others) so that make more difficult for the potential hacker to impersonate someone.
Sometimes, in this statement assessment, it is considered an additional sub-statement:
- Bob is real
This sub-statement can be coherent-detected by extracting some inter-data information about Bob's identity (like the fingerprint, the face or whatever) and checking if it makes sense one another (e.g. if Bob is tall, he should have a big fingerprint). Some current systems implements this action to make the system even more accurate.

To meet these conditions, the provided data must have at least one data with uniqueness and another data (or the same) with privacy. For example, if the provided data has some column which is a unique value, the entire entry combination will be unique. If the provided data has some column which is a private value, the entire entry combination will be private:

| username: unique | password: private | (username, password): unique and private |
| :--: | :--: | ---- |
| john | *** | (john, ***) |
| paul | ** | (paul, **) |
| george | ******** | (george, ****) |
| ringo | ********** | (ringo, **) |

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
As data is non-unique, the server cannot check if a new user already exists. Therefore, register process is not performed and database makes no sense. Only a coherence-detection to check if user is real can be executed (see below).

At the end, the resulting combination properties can be interpreted as an ```OR``` operation. If we define:
- Non-unique: ```0```, Unique: ```1```
- Public: ```0```, Private: ```1```
Then, if we have a data that is, for example, non-unique and private (```01```) and another that is Unique and private (```11```), the resulting combination is (```01 + 11 = 11```), i.e. unique and private. What is the same; to get a ```11``` unique and private value combination, some data must be unique and some data (not necessary the same) must be private.

Other examples are: fingerprints, voice features or face features, which are both unique and private. You can make an authentication system based only on fingerprints, for example, but remember the more data you ask for the user the more accurate the liar-detector is.
Now, maybe you have noted that, to introduce yourself on the Internet, you have to provide something that is **private**. Unless you encrypt it, anyone can steal it! That's why authentication is something that implicitly must go-in-hand with [[#Confidentiality]].
###### Login process

![[LogInProcess.drawio.png]]

The second case involves checking ```a known person == who they claim to be```. This statement might be similar, but it is really opposed to the previous one. Now, Bob would say ```Hi, I am Bob```. Here, Bob is implicitly saying:
- That we should know him
- That he is Bob
This is what happens in **log in process** of an existing user in a server. In this case, we check if we indeed know Bob. We search in database for Bob, but **now if Bob already exists, Bob is saying the truth!** (unlike the previous case), and if Bob doesn't exist, he is a liar because we don't know him. If Bob exists, that means that previously Bob was authenticated in the registering process, so it's not necessary to check whether Bob is Bob, because he is trusted.
In this case, if provided data is private only, but non-unique, the server has no database and the registering process doesn't exist. As a result, there is only log in process, so every user is trusted. To avoid these security risks, at least a coherence-detection can be performed to check if the user is real or not by checking the coherence between provided data.

![[LogInProcessNoDB.drawio.png]]
Note that these authentication methods always let users access to the service. Either with database or not, once the user data is successfully coherent-checked, the user has got access to the service. That's why, in these cases, we must grant permissions through [[#Authorization]] methods apart from the Authentication.
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
### Authorization
It involves preventing hackers from accessing to services they don't have permission. Authorization involves, summarizing, providing mechanism to grant certain permissions to certain users. To do so, we must create a database of **authorized users** or use [[#Certificates]].
#### Trust
Authorization is close-related with **trust**. Only trusted users can have access to the service.
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
Whenever we want to provide [[#Authorization#Trust]], certificates come into play. Certificates are a way to turn someone into a trusted user or machine. 
In practice, certificates can be for example:
- `.p12` files
- `.pem` files
- `.key` and `.crt` files

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
