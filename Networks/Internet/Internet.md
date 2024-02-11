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
### Client-Server communication
- **Server**: a running process which is usually indefinitely awaiting for connections and which provides a service to the client (e.g. a webpage)
- **Client**: the process that wants to be served and sends requests to the server (e.g. our web browser-background process, when we are surfing the internet)
### Peer-to-Peer communication


# IANA
_Internet Assigned Numbers Authority_
# Cybersecurity
The three main parts of cybersecurity are:
- **Availability**
- **Integrity**
- **Confidentiality**
_Known as CIA triad
![[CIA-triad.png]]

But we add in **Authentication**, because it is another important part of cybersecurity that is not covered by CIA triad. Not every points of cybersecurity are addressed by CIA triad. The extended CIA triad covers more issues about cybersecurity:
![[Extended-CIA-triad.png]]
## Availability
All network resources must be available for all users.
## Authentication

![[spiderman-authentication-meme.png]]

The authentication is a complex and philosophical definition about the reality. The definition of authentication is **double**; either one or the other are valid:
- _The process of verification that someone **we don't know before** is who they claim to be_
- _The process of verification that someone **we know before** is who they claim to be_
It's important to take into account both of them, because they are opposed as we will see [[#Philosophical definition|later]]. In both cases, we have to detect a lie about the identity of the person and determine if they are saying the truth about themselves.
_Spoiler:_
- **Bad news**: you will never trust anyone on the Internet, as you can **never** know for 100% certain who the user behind the computer really is
- **Good news**: at least you can make more difficult to the user to lie by forcing them to send you as much information about their identity as you can
### Philosophical definition
In our life, there are statements we can determine with 100% of veracity score, but some others not. The statements are conditions under test (e.g. whether the sky is blue: ```sky == blue```) and, to check their veracity score, a coherent-detection algorithm must be performed. For instance, if someone tells you that they saw yesterday a blue dog, the only way to believe it is seeing it by your own, but that's impossible. Well, the truth is that even if you see it, it can be false, as the dog might have dyed hair. The truth is that only in mathematics field the statements can be 100% true, but not in reality: what you see, smell, feel, touch... Might not be real with 100% certain in a rigorous sense.

_So every statement that you are given must be under test because it might be a lie, and the coherent-detection must be performed so as to determine the veracity score and decide if you trust or not._
#### Coherence-detection analysis
_It is like a mentalist-liar-detector that asks things to liars_
It serves for determining the veracity score for a given statement.
For example, someone may lie you saying that "They work for Google company". Here, the statement under test is that ```job == Google```.
The coherence-detection involves:
1. Getting additional information about the statement (e.g. the country where they are from: ```country=...```, or their job card information: ```job_card=...```). The more related to the issue of the statement, the better information quality to check the lie. Obviously, you won't try to get useless information such as the color of their clothes
2. Check if that additional information is **coherent** with reality. For example, if that person claims to work for Google company and lives in a country where there are no Google offices, you will become suspicious of them. For each information you get, **sub-statements** are assessed: in the example, we want to check whether the person lives in a country with Google offices, whether the person has a good salary or whatever, to determine the final veracity score. The more conditions you assess (the more data you get), the more rigorous you are to trust someone.

```
# Check if the person works for Google = Check all of these sub-conditions:
if(works_in_country_with_google_offices(suspicious_person.country) == True && has_good_wage(suspicious_person.wage) == True && ...){
	I believe them
} else{
	I don't believe them
}
```

##### Information quality
Now, we will define ```ìnformation quality = number of data*information distance to the lie issue```. This value determines if the coherence-detection system is accurate or not with its output: the veracity score. The more information quality, the more accurate the veracity score is (e.g. a coherence detector with a low information quality is not reliable and if the output is equal to ```0.75```, that measure is not reliable).
- The more variables under test, the more difficult for the liar to lie, because they have to create a "coherent story" with all those variables
- And if those variables are closer to statement, the information quality is better and more difficult for the liar to lie
To make a very accurate coherence detector (i.e. a detector that is very accurate with veracity measure, i.e. a good lie-detector), you now know that you must obtain as much information as you can and not only that, but also information the most related to the statement under test as possible.
The coherence-detector is like a mentalist who ask questions to liars to determine whether they say the truth or not. A good mentalist would ask a lot of accurate questions.

When we are talking about authentication, there are two statements under test:
- _The process of verification that someone **we don't know before** is who they claim to be
- _The process of verification that someone **we know before** is who they claim to be_
This means: authentication involves checking either ```a new person == who they claim to be``` or ```a known person == who they claim to be```. Although authentication is both definitions, in practice both definitions are separate conditions under test (i.e. separate lies we have to verify).

The first case involves checking ```a new person == who they claim to be```. To make a human simile, It's as if someone gets closer to you and says ```Hi, I introduce myself: mi name is Bob```. In this statement, the suspicious Bob claims two sub-statements:
- That we don't know him
- That he is Bob
Well, that's what happens in the **register process** of a new user in a server, for example. The user introduces itself providing as much information as it is forced to give. Server designers decides to request all that information to make the authentication system more robust (remember information quality). That information is close to the statement under test: ```a new person == who they claim to be```. Therefore, it is:
- The fingerprint
- The face features
- The password
- The username
- The ID Card...
In this way, if Bob is a liar about his identity, it would be more difficult for him to lie when he introduces himself, as he needs to provide a lot of information (but not impossible).
Now, Bob has also claimed that he is **new** for us and that consequently we don't know him. This is another part of the statement that we must also check. To know whether Bob is known for us, the coherence-detection system, apart from the identity data, **performs a trusted-people-database search using all the provided information** (to check the uniqueness of Bob). If that user already exists, Bob is a liar because there can't be two Bobs, so we decide to believe the Bob who is already registered instead of the new Bob.
Sometimes, in this statement assessment, it is considered an additional sub-statement:
- Bob is real
This sub-statement can be coherent-detected by extracting some information about Bob's identity (like the fingerprint, the face or whatever) and checking if it makes sense one another (e.g. if Bob is tall, he should have a big fingerprint). Some current systems implements this action to make the system even more accurate.

The second case involves checking ```a known person == who they claim to be```. This statement might be similar, but it is really opposed to the previous one. Now, Bob would say ```Hi, I am Bob```. Here, Bob is implicitly saying:
- That we should know him
- That he is Bob
This is what happens in **log in process** of an existing user in a server. In this case, we check if we indeed know Bob. We search in database for Bob, but **now if Bob already exists, Bob is saying the truth!** (unlike the previous case), and if Bob doesn't exist, he is a liar because we don't know him. If Bob exists, that means that previously Bob was authenticated in the registering process, so it's not necessary to check whether Bob is Bob, because he is trusted.
## Integrity
It means that your data cannot be manipulated by someone during the way. On Internet, hackers can manipulate your packet to change the destination, for example. **However, integrity cannot prevent hackers from manipulating the bit order of the packets you send**.
integrity can be addressed by [[#Hashing]] and [[#Encryption]].
## Confidentiality
It involves preventing hackers from accessing the network information that you send to others. However,**It doesn't prevent hackers from sending you information.**
Confidentiality can be addressed by using [[#Encryption]].
## Encryption
### Asymmetric encryption
It consists on [[#Public key]]-[[#Private key]] pair.
When you send information to some destination, your data must be encrypted using the [[#Public key]]. Then, destination can decrypt it using their [[#Private key]]. If you don't have destination's [[#Public key]] key, you cannot encrypt your data to talk with them, so **be careful**. Your security depends on the other's decision.
When you receive information from destination, it must be encrypted too, but using your [[#Public key]]. Thus, you can decrypt it with your [[#Private key]].
#### Public key
The public key, as it name suggests, is sharable to other users. You only must share it to the other part of the communication, which is a trusted host. However, it can be filtered and anyone might have it. The main purpose of this key is to tell others how they must encrypt their data so that you can decrypt it.
_The public key is the data you use to perform some operation over the data you transmit/receive. That operation is always the inverse of the corresponding private key._
#### Private key
The private one is **not** sharable, and you must keep secure on your system.
_The private key is the data you use to perform some operation over the data you transmit/receive. That operation is always the inverse of the corresponding public key._
#### What happens if a hacker gets your public key?
Then the hacker will be able to send you messages, but it is not a security risk.
#### What would happen if you use your private key to encrypt your data?
Then, anyone with your [[#Public key]] (i.e. anyone on the Internet) might access to the information you send, such as credit card, passwords or whatever, so don't do it.
## Hashing
Involves creating a single number that is an operation result applied to the data you send. Therefore, that number should be unique for the binary data you send around Internet. In this way, if a hacker manipulates your network data, the number no longer corresponds to the manipulated data and the recipient of the information would realize.
## Certificates
Certificates are used for becoming reliable to others, or to rely on others. For instance, if you receive information from someone (e.g. a server) on Internet, they give you their identity information


### Could you use your IP address for this purpose?
_Short answer: No_
You may think that, since your [[IP#IP address|IP address]] is unique all over the world, that's enough for authenticating you. However, this is wrong because it is susceptible to be stolen by others as common IP addresses are public for everyone. If there weren't hackers in the world, the answer to this question would be: yes.
### Could you use the private key for this purpose?
_Short answer: No_
You may think that, since you are the only person who own your [[#Private key]], you can use it to provide authentication to others. However, this is not correct, as the private key is not interpretable.
You might think: _Okay, so instead of sharing my private key, let's encrypt my data with my private key and share my public key to others: if they can decrypt it, that means that they can trust me_
This is also wrong, because someone can falsify 

However, when you want to provide [[#Authentication]] to other on Internet (i.e. when you want to be trusted), you must encrypt your data with your [[#Private key]] and send them your [[#Public key]]. Thus, if the destination could decrypt your data, it means that you used the right private key and, as no one has got your private key, then it means that you are who you say to be. In these cases, the data you must encrypt and send is called a **digest**. See [[#Certificates]] to know more about how to provide Authentication with encrypting.
However, what about how you can trust what you receive from others? You need to receive the other's [[#Public key]] to decrypt their data and check whether you can or not decrypt it: if true, you should trust them.
_So, remember, if you want to trust someone, you must have their public key_
But...
#### What happens if someone sends you a fake public key?

With encrypting mechanisms, 

The point here is that, if you could decrypt the data, that means that the origin of the information used the right private key. Therefore, the source [[IP#IP address|IP address]] is correct. However, imagine

## Example algorithms
### RSA
_Rivest-Shamir-Adleman_
Provides 
### AES


