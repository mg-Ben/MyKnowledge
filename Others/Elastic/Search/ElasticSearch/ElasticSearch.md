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
## Indices
It contains several [[#Document|documents]].
### Index template
An index template is a way to tell Elasticsearch how to configure an index when it is created. For example, you can create a template where you specify:
- The number of **index replicas**
- The number of **shards**
- The document fields and data types
- Before creating an index, you must create the template
- Index templates can be specified as [[JSON]] objects
#### Example
```JSON
"template": {
    "mappings": {
      "properties": {
        "@timestamp": {
          "type": "date"
        }
      }
    }
  }
```

## Document
The data that ElasticSearch stores are called _documents_ and can be specified in [[JSON|JSON format]].

## Shards

## Data path
For Debian-based installations:
- [[Linux#/var|/var]]/lib/elasticsearch
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
_By default, ElasticSearch is not available from outside; only in localhost_
If you want to expose ElasticSearch so that other users can connect from outside, you must set `network.host` to the [[IP#Network Interfaces#IP address|Network Interface IP address]] that you want to use to receive data and `http.port` where the ElasticSearch will be running on your machine in `elasticsearch.yml` configuration file.
For example, if you want to use all the network interfaces of the host machine where ElasticSearch cluster is running to reach the cluster, set the host to `0.0.0.0`:
```elasticsearch.yml
network.host: 0.0.0.0
http.port: <ES_PORT>
```
##### Example
```elasticsearch.yml
network.host: 1.2.3.4
http.port: 9200
```
Then ElasticSearch would be available in `1.2.3.4:9200` from outside:

![[public_ESIP_ESPORT.png]]
##### 2.1. discovery.type
If you are deploying a single node cluster:
```elasticsearch.yml
discovery.type: "single-node"
```
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
If you want to test the applied configuration, you can perform a [[Useful commands#curl|curl]]:
```shell
curl -X GET https://<ES_IP:ES_PORT> --cacert <path_to_CA.crt> -u username:password
```
- If you set `anonymous` user, you shouldn't need to specify `-u username:password`
#### 4. Security
Before continuing, set this line in `elasticsearch.yml`:
```elasticsearch.yml
xpack.security.enabled: true
```
In the current versions of ElasticSearch, this parameter is set to `true` by default even if you have commented it out in `elasticsearch.yml`. If you want to disable it, set it explicitly to `false`.
##### HTTPS (TLS/SSL for HTTP) with xpack.security
Remember that, with [[Cybersecurity#Certificates]], we can provide trust in communication. In this case, ElasticSearch will act as a remote server, so we must provide the mechanisms so that clients that will connect to our ElasticSearch instance trust it.
- Therefore, we need to [[#TLS/SSL - Transport Layer Security|generate a TLS/SSL certificate]] that will be sent from ElasticSearch to others so that others can trust ElasticSearch
- As the **TLS/SSL certificate** may have been generated from any Certificate Authority, we must ensure that those to whom we are sending that TLS/SSL certificate from ElasticSearch really trust our Certificate Authority
###### TLS/SSL - Transport Layer Security
Generate the Transport-Layer [[Cybersecurity#Certificates|certificate]]. To do so, use the [[#elasticsearch-certutil]] tool and:
- [[#Generate Certification Authority|Generate Certification Authority for ElasticSearch]]
- [[#elasticsearch-certutil#Generate the Transport-Layer certificate for the node|Generate the Transport-Layer certificate]]
- Configure `elasticsearch.yml` to use this Transport-Layer certificate:
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

Now your ElasticSearch node is secured with TLS/SSL!
###### HTTPS - Application Layer Security
Generate the Application-Layer [[Cybersecurity#Certificates|certificate]]. To do so, use the [[#elasticsearch-certutil]] tool and:
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
- Move the `.pem` file to [[Kibana#Configuration Files Path|kibana config directory]]
###### Change file owner for elasticsearch.keystore
Once you have configured both TLS/SSL and HTTPS certificates, you must [[GNU#chown (change ownership)|change ownership for elasticsearch.keystore file]] to `elasticsearch` user. For example:
```shell
sudo chown elasticsearch: /etc/elasticsearch/elasticsearch.keystore
```
#### 5. Check cluster health
Sometimes it takes a lot of time to prepare [[#Shards]], so you can find errors related to `missing shards` while ElasticSearch is starting. To check cluster health and know the percentage of total shards which are ready, run the following command:
```shell
curl -X GET https://<ES_IP:ES_PORT>/_cluster/health?pretty --cacert <path_to_CA.crt> -u username:password
```
# Hands on
_The best way to learn ElasticSearch is firstly [[#ElasticSearch Locally|work locally]] (without docker containers). Once everything is configured, you can [[#ElasticSearch on Docker Container|dockerize it]]._
## ElasticSearch Locally
### Download ElasticSearch
- You can download ElasticSearch [[BinaryFile|Binary Files]] from [here](https://www.elastic.co/downloads/elasticsearch). Extract the ```.tar.gz``` file, look for ```/bin``` directory
- However, it's recommended to install ElasticSearch with Debian Package (refer to [Install Elasticsearch with Debian Package | Elasticsearch Guide [8.13] | Elastic](https://www.elastic.co/guide/en/elasticsearch/reference/current/deb.html)), as the configuration files will be automatically placed where they should be. Remember the [[Linux#APT|steps]] for installing Debian packages
### Configure ElasticSearch
Go to [[#Configuration Files Path]].
There are some [[#Important configurations for a ElasticSearch Node]] you can do before deploying ElasticSearch.
### Run ElasticSearch = Run one ElasticSearch node
Depending on how you installed ElasticSearch:
- If you installed it as a binary file, [[BinaryFile#How to run|Run elasticsearch binary file]]. After running it, it will take the configuration from ```/config``` directory inside the [[#Download ElasticSearch|extracted folder]]
- If you installed it as a service (with Debian Package), [[Linux Service#Start service|start elasticsearch.service]]. If you find some error logs related to ElasticSearch upgrade:

	_org.elasticsearch.gateway.CorruptStateException: Format version is not supported. Upgrading to [8.12.2] is only supported from version [7.17.0]_

	You will need to [[UNIX#rm (Remove files or directories)|remove]] the [[#Data path|elasticsearch data]], [[Linux#APT#Uninstall package|purge the elasticsearch package]] and [[Linux#APT#Install a Debian package|install elasticsearch again]]
## ElasticSearch on Docker Container
### Create or download the ElasticSearch Image
You can either create a custom image through [[Docker#Dockerfile Example|Dockerfile]] from the ElasticSearch official image (setting ```FROM elasticsearch...```) or directly use the ElasticSearch official image by [[Docker#Interesting commands#Build docker image#From DockerHub|pulling it]].
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


## ElasticSearch Tools
ElasticSearch installation comes with several additional tools. They are located in `/bin`:
- For `.tar`, `.gz` or `.zip` installation, the `/bin` is inside the extracted folder
- For Debian-based installation, the `/bin` is in `/usr/share/elasticsearch/bin`
#### elasticsearch-reset-password
For resetting passwords for users, such as Kibana user.
1. Disable all the configurations made for [[#4. Security]] if you had. This implies:
	1. Disable all the [[#4. Security]] settings inside ```elasticsearch.yml``` and remove all passwords inside the [[#elasticsearch-keystore]]
	2. [[#Run ElasticSearch = Run one ElasticSearch node|Run again your ES node]]
	3. While running, run the restart password tool: `./elasticsearch-reset-password -u <username>`. If your cluster is in RED state and cannot be started, you can pass the `-f` flag to force reset password
#### elasticsearch-certutil
For generating [[SSL-TLS#elasticsearch-certutil|SSL/TLS certificates]] and encrypt inter-node communication.
##### Generate Certification Authority
_Theory: [[Cybersecurity#Certificate Authorities (CA)]]_
```shell
./elasticsearch-certutil ca
```
- Enter the certificate output file. Please, in this step specify also the extension (e.g.: ```output.p12```).
- Enter a password for the [[Cybersecurity#Certificate Authorities (CA)|CA]]
The output will be a ```.p12``` file with the public-private key of Certification Authority.
Interesting flags:
- ```--pem```: the output will be a ```.zip``` with the certificate in ```pem``` format inside (i.e. one ```.key``` file and one ```.crt``` file)
##### Generate the Transport-Layer certificate for the node
_Theory: [[Cybersecurity#Certificate Authorities (CA)]]. This Transport-Layer certificate will be sent form ElasticSearch to the clients so that they can trust ElasticSearch_
```shell
./elasticsearch-certutil cert --ca <CA.p12>
```
- Enter the [[Cybersecurity#Certificate Authorities (CA)|CA]] path and password
- Enter the certificate output file. Please, in this step specify also the extension (e.g.: ```output.p12```)
The output will be a file named ```elastic-certificates.p12``` by default which is the certificate for one node. That certificate contains the key of the node and both the [[Cybersecurity#Certificate Authorities (CA)|CA]] certificate and the node certificate.
Interesting flags:
- ```--pem```: the output will be a ```.zip``` with the certificate in ```pem``` format inside (i.e. one ```.key``` file and one ```.crt``` file)
- `--dns <DNS (or hostname)>`
You can repeat these steps for each node so that each one has got its own password
##### Generate the Application-Layer certificate for the node
```shell
./elasticsearch-certutil http
```
You will need to enter some configurations such as:
- Generate a CSR (_Certificate Signing Request_). If this certificate is self-signed, select No (N)
- Use an existing CA: Select Yes (y) and enter the [[#Generate Certification Authority|previously generated CA]]. You can also opt for generating a CA at the moment
- Generate a certificate per node. If you choose Yes, you can enter the node name, but you can leave it empty (it is not necessary). However, choose No if you have a single-node cluster
- **DNS names**: these names are the DNS names that others will use to connect to your ES instance (e.g. if you are running ElasticSearch inside some Operating System, you can set this to your [[Virtualization#Guest Operating System|Guest OS]]/[[Virtualization#Host Operating System|Host OS]] [[UNIX#hostname (get DNS name)|hostname]], as well as `localhost`)
- **IP addresses**: these [[IP#IPv4|IP addresses]] refer to those IPs that others will use to connect to your ES instance (e.g. you can set this to [[IP#Special IP addresses]] such as `0.0.0.0`, `127.0.0.1`, `127.0.1.1` and the [[IP]] you get with `ipconfig` or `ifconfig`)
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
## Explore data
### Check index health
You can both [[#5. Check cluster health|check ES cluster health]] and check the health for a specific index with:
```shell
curl -X GET http(s)://ES_IP:ES_PORT/_cluster/health/<index> [other_curl_options]
```
### Delete index
```shell
curl -X DELETE http(s)://ES_IP:ES_PORT/<index> [other_curl_options]
```
### Get all indices
Retrieve ElasticSearch [[#Indices]]:
```shell
curl -X GET http(s)://ES_IP:ES_PORT/_cat/indices [other_curl_options]
```
### Get data inside an index
```shell
curl -X GET http(s)://ES_IP:ES_PORT/<index>/_search [other_curl_options]
```
### Query data
_Refer to [The search API | Elasticsearch Guide [8.13] | Elastic](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-your-data.html)_
To query ElasticSearch data, you will need to use a [[HTTP#REST|REST]] [[Internet#API|API]] towards `ES_IP:ES_PORT` through [[HTTP]]. The structure of the query is the following:
```
GET http(s)://ES_IP:ES_PORT/<index>/_search
{
	<QueryDSL-Body>
}
```
Queries can be performed in two contexts:
- **Query context**: We write the query and ElasticSearch will calculate how well each document matches the query, sorted in descending order by some score value
- **Filter context**: We write the query and ElasticSearch will return only the documents that fulfill the query
#### Query DSL
_Refer to [Query DSL | Elasticsearch Guide [8.13] | Elastic](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl.html)_
The query is [[DSL]] and it is based on [[JSON]].
### Handy commands
- Iterate over all the ElasticSearch indices and, for each one, see if there is something inside the index that is related to some string:
```shell
sudo curl -s http(s)://ES_IP:ES_PORT/_cat/indices [other_curl_options] |
while read -r line; \
do \
	read -a array <<< $line; echo ${array[2]}:\;
	sudo curl -s http(s)://ES_IP:ES_PORT/${array[2]}/_search?pretty [other_curl_options] |
	grep <some_string>;
done
```
### Index notation
- If the index begins with `.`, it is a self-managed index (managed by ElasticSearch itself).
- `ds = data stream`
# References
- [Elastic Products - Entry Point of Documentation](https://www.elastic.co/guide/index.html)