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
Where the passwords can be securely stored and cannot be read (the content of this file is encrypted).
To add passwords, use the [[#Run ElasticSearch = Run one ElasticSearch node#elasticsearch-keystore|elasticsearch-keystore]]. Everything that you can set in this file is equivalent to set in [[#Main Configuration Files|elasticsearch.yml]]. However, you may not want to see them as plain text (e.g. if you want to set some password inside `elasticsearch.yml`). Therefore you can set those configurations in the keystore.
### Configuration Files Path
#### For locally running ElasticSearch
##### For Debian installation
Refer to Debian Directory Layout [Install Elasticsearch with Debian Package | Elasticsearch Guide [8.13] | Elastic](https://www.elastic.co/guide/en/elasticsearch/reference/current/deb.html#deb-layout).
You will typically find the ElasticSearch files in the following path:
```
/etc/elasticsearch
```
##### For Binaries (.tar.gz or .zip) installation
But if you are running ElasticSearch in some directory because you are testing it (e.g. you are running ElasticSearch in Downloads folder), by default the configuration files path would be:
```
<path_to_extracted_elasticsearch>/config
```
#### For Docker-deployed ElasticSearch
You will find the ElasticSearch files in the following path:
```
/usr/share/elasticsearch/config
```
### Important configurations for a ElasticSearch Node
#### 0. General configurations
_Node names, cluster name..._

#### 1. Linux Users: kibana and elasticsearch
By default, when you install ElasticSearch or Kibana in your system (and for Debian installations), you will find that two new [[Linux#Users]] have been implemented: `elasticsearch` and `kibana`. These users are **Linux Operating System users** and are not the same as the [[#1. Kibana and Elastic user]]. The first ones serve for managing ElasticSearch and Kibana files inside Linux.
Whenever you need to run some command which requires `elasticsearch` or `kibana` user, you can either [[Linux#Switch user]] or [[Linux#Run some command as another user|Run the command as kibana or elasticsearch user]]. For example, `elasticsearch` user is needed to manage [[#elasticsearch.keystore file]].
#### 2. Exposing ElasticSearch outside
If you want to expose ElasticSearch so that other users can connect from outside, you must set `network.host` to the [[IP#Network Interfaces#IP address|Network Interface IP address]] that you want to use to receive data in `elasticsearch.yml` configuration file. For example, if you want to use all the network interfaces of the host machine where ElasticSearch cluster is running to reach the cluster:
```elasticsearch.yml
network.host: 0.0.0.0
```
You might need to set `discovery.type` too.
#### 3. Application users: kibana_system and elastic
_By default, there exists a user inside ElasticSearch configuration which is the **kibana_system** user. This is to allow kibana access ElasticSearch.
On the other hand, there is another user inside ElasticSearch configuration which is **elastic** user. You can use this user to log in to ElasticSearch._
##### Through elasticsearch-reset-password tool
_This method requires the ElasticSearch cluster to be running and active_
You might want to interconnect [[Kibana]] to ElasticSearch. To do so, before [[#Security|configuring ElasticSearch security]], you must obtain a Kibana password. In practice, Kibana is like a user for ElasticSearch. When you run ElasticSearch for the first time, a Kibana password is generated. However, if you didn't write down the password or you have forgotten it, you must regenerate it with [[#elasticsearch-reset-password]].
Write down the password and set it in [[Kibana#1. Username and Password for ElasticSearch|kibana.yml file]].
On the other hand, do the same with `elastic` user.
##### Anonymous user
You can also disable authentication to ElasticSearch by implementing anonymous user. For this purpose, make the following settings in `elasticsearch.yml` configuration file:
```YAML
xpack.security.authc: 
  anonymous: 
    username: anonymous_user 
    roles: superuser 
    authz_exception: true
```
However, once this setting has been made, this doesn't imply that you can access with any `user:password`. This setting only serves for those cases you don't have a user to access ElasticSearch (such as [[#Elastic Agent|Elastic Agents]]), as it sets a new user called `anonymous` for this purpose. However, even with this configuration you might want to define other users which require authentication such as kibana_system and elastic users. In this case, you must provide the corresponding passwords.
##### Test user authentication
If you want to test the applied configuration, you can perform a `curl`:
```shell
curl -X GET https://<ES_IP:ES_PORT>
```
#### 5. Security
Before continuing, set this line in `elasticsearch.yml`:
```elasticsearch.yml
xpack.security.enabled: true
```
##### HTTPS (TLS/SSL for HTTP) with xpack.security
###### TLS/SSL - Transport Layer Security
Generate the Transport-Layer `.p12` [[Cybersecurity#Certificates]]. To do so, use the [[#elasticsearch-certutil]] tool and:
- [[#Generate Certification Authority]]
- [[#elasticsearch-certutil#Generate the Transport-Layer certificate for the node|Generate the Transport-Layer certificate]]
- Configure `elasticsearch.yml` to use this certificate:
```elasticsearch.yml
xpack.security.transport.ssl:
  enabled: true
  verification_mode: certificate
  keystore.path: <path to your .p12 TLS certificate file>
  truststore.path: <path to your .p12 TLS certificate file>
```
- [[#Add password to keystore|Store the node password in elasticsearch.keystore]]. For this purpose, run it twice (for both, enter the node password you chose when you generated the Transport-Layer certificate):
1. Specifying as ```configuration_name = xpack.security.transport.ssl.keystore.secure_password``` 
2. Specifying as ```configuration_name = xpack.security.transport.ssl.truststore.secure_password```
###### HTTPS - Application Layer Security
Generate the Application-Layer `.p12` [[Cybersecurity#Certificates]]. To do so, use the [[#elasticsearch-certutil]] tool and:
- [[#Generate the Application-Layer certificate for the node]]. The result will also contain the ```.pem``` file for [[Kibana]] so that Kibana trusts this ElasticSearch `.p12` certificate.
- Configure `elasticsearch.yml` to use this certificate:
```elasticsearch.yml
xpack.security.http.ssl:
  enabled: true
  keystore.path: <path to your .p12 HTTP certificate file>
```
- [[#Add password to keystore|Store the node password in elasticsearch.keystore]]. For this purpose, run it once (enter the password you chose for the node when generated the Application-Layer certificate):
1. Specify as ```configuration_name = xpack.security.http.ssl.keystore.secure_password``` 
2. Enter the password
- Move the `.pem` file to [[Kibana#Configuration Files Path|kibana config directory]].
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
Similarly to [[SNMP#Core principles|SNMP Agent]], the Elastic Agent is a process that runs on the target machine [[Internet#Terminal systems|host]] that we want to monitor.
Unlike [[SNMP]], the agent communicates directly to the database (ElasticSearch) as depicted here; there is no an analogous to [[SNMP#Core principles|SNMP Manager]]:
![[ElasticAgentCorePrinciples.png]]
_Elastic Agent can monitor the host where it’s deployed, and it can collect and forward data from remote services and hardware where direct deployment is not possible_
However, not every service can be exposed by the Elastic Agent: that service must be an [[#Integration]].
##### Integration
_Integration = What we want to monitor (e.g. The "System" integration is for monitoring system data such as CPU load)_
For example, here you can see some integrations. They are like "plugins" you can implement to monitor the target services:

![[integration-examples.png]]

There is a **Fleet Server** integration to manage Fleet Servers.
Therefore, integrations provide the bridge to manage different services:
![[ElasticAgentCorePrinciplesIntegrations.png]]
Use **Elastic Package Registry** to download Integrations packages.
#### Policy
Consists of N [[#Integration|Integrations]].
![[1_policy_N_integrations.png]]

To specify which [[#Integration|integrations]] you want to run and on which hosts. This means that you might have more than one host and each host may need different services to monitor:
![[ElasticAgentCorePrinciplesPolicies.png]]
You can even apply the same 1 policy to N Agents.
##### Fleet mode
You configure the policies through [[#Fleet]] User Interface (easy way).
##### Standalone mode
You specify the policies in a [[YAML]] file (more advanced).
### Fleet Server
It is an [[#Elastic Agent]] but configured as a **Fleet Server** (i.e. enrolled in a [[#Policy]] called **Fleet Server**).

![[FleetServer_Agents.png]]

#### Fleet
It is available in [[Kibana]]. It involves an UI (User Interface) to explore agents.
### Hands on
You have to follow these steps, in this order:
1. [[#Deploy the Fleet Server]]
2. [[#Deploy the Elastic Agent]]
#### Deploy the Fleet Server
##### On-premises (self-managed)
Refer to [Deploy on-premises and self-managed | Fleet and Elastic Agent Guide [8.12] | Elastic](https://www.elastic.co/guide/en/fleet/8.12/add-fleet-server-on-prem.html)
1. Get a SSL-TLS certificate
This is needed to guarantee the Authentication and Confidentiality of [[SSL-TLS]].
##### Cloud-managed

#### Deploy the Elastic Agent
##### Install Elastic Agent
###### Windows
Run 
##### Uninstall Elastic Agent
###### Windows
Run `C:\"Program Files"\Elastic\Agent\elastic-agent.exe uninstall`. You must be on the [[Windows#CMD]] as Administrator (otherwise the command won't be recognized by cmdlet) and outside the `C:\"Program Files"\Elastic` directory (i.e. in `C:\"Program Files"`).
- If `elastic-agent.exe` file is missing, you will have to [[#Install Elastic Agent#Windows]] again and let the process create the `.exe` file.
###### Linux
`sudo ./usr/bin/elastic-agent uninstall`
If the command output is:
`Error: can only be uninstalled by executing the installed Elastic Agent at: /usr/bin/elastic-agent`
Then it means that the Elastic-Agent is partially installed and broken. To solve this issue:
1. Stop the current `elastic-agent.service` with `sudo systemctl stop elastic-agent.service`
2. Disable the current `elastic-agent.service` with `sudo systemctl disable elastic-agent.service`
3. Remove the `elastic-agent.service` file with `sudo rm /etc/systemd/system/elastic-agent.service`
4. Remove the `/opt/Elastic/Agent` directory with `sudo rm -r /opt/Elastic/Agent`
##### Local

##### Docker
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

# CLOUD: PROVISION, MANAGE AND MONITOR THE ELASTIC STACK

# Hands on
_The best way to learn ElasticSearch is firstly [[#ElasticSearch Locally|work locally]] (without docker containers). Once everything is configured, you can [[#ElasticSearch on Docker Container|dockerize it]]._
## ElasticSearch Locally
### Download ElasticSearch
- You can download ElasticSearch [[BinaryFile|Binary Files]] from [here](https://www.elastic.co/downloads/elasticsearch). Extract the ```.tar.gz``` file, look for ```/bin``` directory.
- However, it's recommended to install ElasticSearch with Debian Package (refer to [Install Elasticsearch with Debian Package | Elasticsearch Guide [8.13] | Elastic](https://www.elastic.co/guide/en/elasticsearch/reference/current/deb.html)), as the configuration files will be automatically placed where they should be. Remember the [[Linux#APT|steps]] for installing Debian packages.
### Configure ElasticSearch
Go to [[#Configuration Files Path]].
There are some [[#Important configurations for a ElasticSearch Node]] you can do before deploying ElasticSearch.
### Run ElasticSearch = Run one ElasticSearch node
[[BinaryFile#How to run|Run elasticsearch binary file]]. Remember: [[BinaryFile#As a command| you can turn any binary file into a command on your Bash]] and you can even [[Linux Service#Daemonize Bash command|Daemonize it]].
After running it, it will take the configuration from ```/config``` directory inside the [[#Download ElasticSearch|extracted folder]].
Inside the ```/bin``` directory, you will also find some useful elasticsearch tools. For Debian-based installation, the `/bin` is in `/usr/share/elasticsearch/bin`. These tools are:
#### elasticsearch-reset-password
For resetting passwords for users, such as Kibana user.
1. Disable all the configurations made for [[#2. Security|Security]] if you had. This implies:
	1. Disable all the [[#2. Security|Security]] settings inside ```elasticsearch.yml``` and remove all passwords inside the [[#elasticsearch-keystore]].
	2. [[#Run ElasticSearch = Run one ElasticSearch node|Run again your ES node]].
	3. While running, run the restart password tool: `./elasticsearch-reset-password -u <username>`
#### elasticsearch-certutil
For generating [[SSL-TLS#elasticsearch-certutil|SSL/TLS certificates]] and encrypt inter-node communication.
##### Generate Certification Authority
```shell
./elasticsearch-certutil ca
```
- Enter the certificate output file. Please, in this step specify also the extension (e.g.: ```output.p12```).
- Enter a password for the CA
The output will be a ```.p12``` file with the public-private key of Certification Authority.
Interesting flags:
- ```--pem```: the output will be a ```.zip``` with the certificate in ```pem``` format inside (i.e. one ```.key``` file and one ```.crt``` file)
##### Generate the Transport-Layer certificate for the node
```shell
./elasticsearch-certutil cert --ca <CA-public-private-keys.p12>
```
- Enter the CA path and password
- Enter the certificate output file. Please, in this step specify also the extension (e.g.: ```output.p12```)
The output will be a file named ```elastic-certificates.p12``` by default which is the certificate for one node. That certificate contains the key of the node and both the CA certificate and the node certificate.
Interesting flags:
- ```--pem```: the output will be a ```.zip``` with the certificate in ```pem``` format inside (i.e. one ```.key``` file and one ```.crt``` file)
You can repeat these steps for each node so that each one has got its own password.
##### Generate the Application-Layer certificate for the node
```shell
./elasticsearch-certutil http
```
#### elasticsearch-keystore
For adding passwords to [[#Configuration Files#elasticsearch.keystore file]].
The [[Linux#File owner]] of the elasticsearch-keystore may be `elasticsearch` user. You can determine the File Owner with [[UNIX#ls (List files)]] inside the [[#Configuration Files Path]]:
![[file-owner-keystoreES.png]]
If you don't have permissions to modify this file, you can [[#Generate a new keystore file]] whose owner is `root` user.
##### Generate a new keystore file
```shell
sudo ./elasticsearch-keystore generate
```
##### Add password to keystore
```shell
./elasticsearch-keystore add <configuration_name>
```
For example:
- ```configuration_name = xpack.security.transport.ssl.keystore.secure_password```
You will be prompted to add the password value.
##### Show stored password
```shell
./elasticsearch-keystore show <configuration_name>
```
##### Remove pasword to keystore
```shell
./elasticsearch-keystore remove <configuration_name>
```
For example:
- ```configuration_name = xpack.security.transport.ssl.keystore.secure_password```
You will be prompted to add the password value.
##### List paswords inside keystore
```shell
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
- **Through Bind-mount**: you can create your entire and own [[ElasticSearch#Configuration Files]] (such as ```elasticsearch.yml``` file) in your [[Virtualization#Host Operating System|Host OS]] and [[Docker#Bind-mount approach|Bind-mount it]] from the directory where you have your [[ElasticSearch#Configuration Files]] to the [[ElasticSearch#Configuration Files Path#For Docker-deployed ElasticSearch|Configuration Files Path inside Docker container]]. The changes you make to the configuration files will be real-time synced with the internal bind-mounted configuration files in the container. Once you have modified those configuration files, you must [[Docker#Restart your container]] so that those changes make effect. If you defined your containers in a `docker-compose.yml` file, remember that you can [[Docker#Restart docker containers defined in docker-compose.yml]].
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