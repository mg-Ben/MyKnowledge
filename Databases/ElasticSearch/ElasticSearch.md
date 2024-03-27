---
tags:
  - Databases
---
# Core principles
A DataBase for **analytics** and **fast searches**. It stores data (called [[#Document|documents]]).
When to use Elasticsearch:
- Webpages and searches within that webpages (e.g. a searchbox)
- Log and event storing
- Machine Learning
Refer to [[#References | References > Elastic Products - Entry Point of Documentation]] to know more about how this documentation has been structured.
## Document
The data that ElasticSearch stores are called _documents_ and can be specified in [[JSON|JSON format]].
## Configuration Files
Where are they located? Inside [[#Configuration Files Path]].
### Main Configuration Files
ElasticSearch can be mainly configured through three files:
- ```elasticsearch.yml```
- ```jvm.options```
- ```log4j2.properties```
### Other Configuration Files
#### elasticsearch.keystore file
Where the passwords can be securely stored and cannot be read (the content of this file is encrypted). To add passwords, use the [[#Run ElasticSearch|elasticseach-keystore binary tool]].
### Configuration Files Path
#### For locally running ElasticSearch
You will find the ElasticSearch files in the following path:
```
/etc/elasticsearch
```
But if you are running ElasticSearch in some directory because you are testing it (e.g. you are running ElasticSearch in Downloads folder), by default the configuration files path would be:
```
<path_to_extracted_elasticsearch>/config
```
#### For Docker-deployed ElasticSearch
You will find the ElasticSearch files in the following path:
```
/usr/share/elasticsearch/config
```

### Important configurations
#### xpack


# SEARCH
# OBSERVABILITY
# SECURITY
# ELASTIC STACK
# INGEST: ADD YOUR DATA
## Beats
### AuditBeat
### FileBeat
### FunctionBeat
### HeartBeat
### MetricBeat
### PacketBeat
### WinlogBeat
## Elastic Agent + Fleet Server
It servers for monitoring mainly application **logs** and **metrics**.
It is an alternative to [Elastic Beats](https://www.elastic.co/guide/en/beats/libbeat/current/beats-reference.html), that have the same purpose. However, whereas [[#Beats]] are lightweight, you will have to install separate agents for each type of data you want to monitor (one for Metrics, another one for logs, another one for files...).
See [Elastic Agent or Beats?](https://www.elastic.co/guide/en/fleet/current/beats-agent-comparison.html) to know which option is better.
### Elastic Agent
Similarly to [[SNMP#Core principles|SNMP Agent]], the Elastic Agent is a process that runs on the target machine [[Internet#Terminal systems|host]] that we want to monitor. Then, the [[#Fleet Server]] will make requests to retrieve some data of this agent.
However, this Elastic Agent is also capable of monitoring external host data (from other machines), as it is depicted here:
![[ElasticAgentCorePrinciples.png]]
_Elastic Agent can monitor the host where it’s deployed, and it can collect and forward data from remote services and hardware where direct deployment is not possible_
However, not every service can be exposed by the Elastic Agent: that service must be an [[#Integration]].
##### Integration
To connect your data to Elastic Stack. Basically it is the bridge that connects a service with the Agent.
![[ElasticAgentCorePrinciplesIntegrations.png]]
Use **Elastic Package Registry** to download Integrations packages.
#### Policy
To specify which integrations you want to run and on which hosts. This means that you might have more than one host and each host may need different services to monitor:
![[ElasticAgentCorePrinciplesPolicies.png]]
You can even apply 1 policy to N Agents.
##### Fleet mode
You configure the policies through [[#Fleet]] User Interface (easy way).
##### Standalone mode
You specify the policies in a [[YAML]] file (more advanced).

### Fleet Server
_It's not the same as Fleet!_
Similarly to [[SNMP#Core principles|SNMP Manager]], the Fleet Server is a process that runs on the machine [[Internet#Terminal systems|host]] that monitors the [[#Elastic Agent]] by making requests to that running agent.
You need to connect to that Fleet Server in order to see application data. One way is through [[#Fleet]].
#### Fleet
It is available in [[Kibana]]. It involves an UI (User Interface) to explore agents.
### Hands on
#### Create a service to be monitored
#### Deploy the Elastic Agent on Windows as Docker Container
Refer to [Run Elastic Agent in a container | Fleet and Elastic Agent Guide [8.12] | Elastic](https://www.elastic.co/guide/en/fleet/current/elastic-agent-container.html).
You can run the Elastic Agent as a [[Docker|Docker container]]. In the configuration file, you have to set that the agent will enroll to the [[#Fleet Server]] (i.e. will subscribe to it) so that the Fleet Server communicate with it, specifying the Fleet Server URL.
Here you can see the template [[Docker#3. The reality docker-compose.yml|docker-compose.yml]] to deploy the Elastic Agent:
```yaml
version: "0.0.0"
services:
  elastic-agent:
    image: docker.elastic.co/beats/elastic-agent:8.12.2
    container_name: elastic-agent
    restart: always
    user: root # note, synthetic browser monitors require this set to `elastic-agent`
    environment:
      - FLEET_ENROLLMENT_TOKEN=<enrollment-token>
      - FLEET_ENROLL=1
      - FLEET_URL=<fleet-server-url>
```
#### Deploy the Fleet Server on a Virtual Machine on-premises (self-managed)
Refer to [Deploy on-premises and self-managed | Fleet and Elastic Agent Guide [8.12] | Elastic](https://www.elastic.co/guide/en/fleet/8.12/add-fleet-server-on-prem.html)
There are several models to deploy a Fleet Server. Summarizing, we find the self-managed model and cloud-managed. Here you have the steps for **self-managed** solution, also called _on-premises_.
##### Get a SSL-TLS certificate
This is needed to guarantee the Authentication and Confidentiality of [[SSL-TLS]].
##### Deploy Kibana as a container
To deploy Kibana, you will need to deploy ElasticSearch before (this is a compulsory action).

# CLOUD: PROVISION, MANAGE AND MONITOR THE ELASTIC STACK

# Hands on
## ElasticSearch Locally
### Download ElasticSearch
You can download ElasticSearch [[BinaryFile|Binary Files]] from [here](https://www.elastic.co/downloads/elasticsearch). Extract the ```.tar.gz``` file, look for ```/bin``` directory.
### Configure ElasticSearch
Go to ```/config``` directory and make the changes you want on the [[ElasticSearch#Configuration Files]].
### Run ElasticSearch
[[BinaryFile#How to run|Run elasticsearch binary file]]. Remember: [[BinaryFile#As a command| you can turn any binary file into a command on your Bash]] and you can even [[Linux Service#Daemonize Bash command|Daemonize it]].
After running it, it will take the configuration from ```/config``` directory inside the [[#Download ElasticSearch|extracted folder]].
Inside the ```/bin``` directory, you will also find some useful elasticsearch tools, such as:
#### elasticsearch-certutil
For generating [[SSL-TLS#elasticsearch-certutil|SSL/TLS certificates]] and encrypt inter-node communication.
##### Generate Certification Authority
```shell
./elasticsearch-certutil ca
```
- Enter the certificate output file. Please, in this step specify also the extension (e.g.: ```output.p12```).
- Enter a password for the CA
The output will be a ```.p12``` file with the public-private key of Certification Authority.

##### Generate the certificate for the nodes
```shell
./elasticsearch-certutil cert --ca <CA-public-private-keys.p12>
```
- Enter the CA password
- Enter the certificate output file. Please, in this step specify also the extension (e.g.: ```output.p12```)
The output will be a file named ```elastic-certificates.p12``` by default which is the certificate for one node. That certificate contains the key of the node and both the CA certificate and the node certificate.
You can repeat these steps for each node so that each one has got its own password.
##### Store the node password in elasticsearch.keystore
[[#elasticsearch-keystore|Run elasticsearch-keystore tool]] to add the passwords to [[ElasticSearch#elasticsearch.keystore file]].
Run it twice. For each, specify the node password you specified in the [[#Generate the certificate for the nodes]] step when prompted:
1. Specifying as ```configuration_name = xpack.security.ssl.transport.keystore.secure_password``` 
2. Specifying as ```configuration_name = xpack.security.ssl.transport.truststore.secure_password```.
##### Move the .p12 node certificate files to ES xpack Path
Move the previously generated [[#Generate the certificate for the nodes|node certificates .p12 file]] to the specified path in the [[ElasticSearch#Main Configuration Files|elasticsearch.yml]]. Specifically, you have to set (if not set yet) or find this configuration line:
```yaml
xpack.security.transport.ssl.keystore.path:<path>
xpack.security.transport.ssl.truststore.path:<path>
```
Where ```<path>``` must be equal to the path where the [[#Generate the certificate for the nodes|node certificates .p12 file]] lies in.
_Note: that path is relative to [[ElasticSearch#Configuration Files Path|ES Conf Files Path]], i.e. if you set ```<path> = node-certificate.p12``` for example, this really means ```/etc/elasticsearch/node-certificate.p12``` in locally running ES or ```/usr/share/elasticsearch/config/node-certificate.p12``` in docker running ES, or ```<path_to_extracted_elasticsearch>/config/node-certificate.p12``` in somewhere locally running ES. If you set for example ```<path> = certs/node-certificate.p12```, this means ```/etc/elasticsearch/certs/node-certificate.p12``` and so on..._
#### elasticsearch-keystore
For adding passwords to [[#Configuration Files#elasticsearch.keystore file]].
##### Add pasword to keystore
```
./elasticsearch-keystore add <configuration_name>
```
For example:
- ```configuration_name = xpack.security.ssl.transport.keystore.secure_password```
You will be prompted to add the password value.
##### Remove pasword to keystore
```
./elasticsearch-keystore remove <configuration_name>
```
For example:
- ```configuration_name = xpack.security.ssl.transport.keystore.secure_password```
You will be prompted to add the password value.
##### List paswords inside keystore
```
./elasticsearch-keystore list
```
## ElasticSearch on Docker Container
### Create or download the ElasticSearch Image
You can either create a custom image through [[Docker#Dockerfile Example|Dockerfile]] from the ElasticSearch official image (setting ```FROM elasticsearch...```) or directly use the ElasticSearch official image by [[Docker#Interesting commands#Get docker image#From DockerHub|pulling it]].
Remember that it is better to define a [[Docker#3. The reality docker-compose.yml|docker-compose.yml]] where the pull is automatically done.
### Configure ElasticSearch
To configure ElasticSearch, you have several ways:
- **Through environment variables**: Leave the default configurations for [[ElasticSearch#Configuration Files]] and only override some of them or add new configurations. For this purpose, you can pass environment variables when [[Docker#Create your container|creating your container]] or directly in the ```environment``` section in [[Docker#Template example|docker-compose.yml]]. For instance, if you want to add some [[ElasticSearch#xpack]] configurations to the ```elasticsearch.yml``` inside the container, you can pass the corresponding environment variables like
```yaml
environment:
	- cluster.name=es-cluster
	- xpack.security.transport.ssl.enabled=true
	- xpack.security.transport.verification_mode=true
```
- **Through Bind-mount**: you can create your entire and own [[ElasticSearch#Configuration Files]] (such as ```elasticsearch.yml``` file) in your [[Virtualization#Host Operating System|Host OS]] and [[Docker#Bind-mount approach|Bind-mount it]] from the directory where you have your [[ElasticSearch#Configuration Files]] to the [[ElasticSearch#Configuration Files Path#For Docker-deployed ElasticSearch|Configuration Files Path inside Docker container]].
If you want to make some security configurations such as add a certificate to a node, bind mount is a good option. In this case, you have to follow the [[#ElasticSearch Locally#elasticsearch-certutil]] steps and then bind mount these files:
```yaml
volumes:
	- path_in_host_OS/node-certificate.p12:/usr/share/elasticsearch/config/node-certificate.p12
	- path_in_host_OS/elasticsearch.keystore:/usr/share/elasticsearch/config/elasticsearch.keystore
```
### Run ElasticSearch
Delete this: this is for dockerized section:


# References
- [Elastic Products - Entry Point of Documentation](https://www.elastic.co/guide/index.html)