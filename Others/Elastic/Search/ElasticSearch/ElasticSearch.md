---
tags:
  - Databases
---
# Core principles
_It is a [[Databases#Non-relational database or NoSQL|NoSQL]] database, so it provides more scalability and flexibility_
A DataBase for **analytics** and **fast searches**. It stores data (called [[#Document|documents]]).
When to use Elas++ticsearch:
- Webpages and searches within that webpages (e.g. a searchbox)
- Log and event storing
- Machine Learning
Refer to [[#References | References > Elastic Products - Entry Point of Documentation]] to know more about how this documentation has been structured.
## Indices
It contains several [[#Document|documents]].
### Index settings
You can access to and modify some index settings such as number of shards or replicas. However, in many cases you will have an index which is created periodically: in that case, you will have to use [[#Index template|index templates]].
### Index pattern
_Refer to [Search API | Elasticsearch Guide [8.15] | Elastic](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-search.html#search-search-api-path-params) and [Multi-target syntax | Elasticsearch Guide [8.15] | Elastic](https://www.elastic.co/guide/en/elasticsearch/reference/current/multi-index.html)
When you want to reference several indexes (e.g. in a [[Grafana#ElasticSearch Datasource|Grafana ElasticSearch Datasource]]), you can specify an index pattern with Wildcards:
- **`*` (Asterisk)**:
    - **Matches zero or more characters**. It can be used anywhere in an index name to match multiple indices
    - Example:
        - `logstash-*` matches all indices starting with `logstash-` (e.g., `logstash-2023-10-24`, `logstash-2023-10-25`, etc.)
        - `*` by itself matches **all indices** in the cluster
- **Braces `{}` for Expansions**:
    - Braces can be used for **expansion** of index names. You can define a set of alternatives inside braces to match multiple patterns
    - Example:
        - `logstash-{2023,2024}-*` matches indices like `logstash-2023-*` and `logstash-2024-*`
        - `logs-{a,b,c}-*` would match indices like `logs-a-*`, `logs-b-*`, and `logs-c-*`
- **Range Patterns**:
    - You can specify ranges using numeric sequences within braces
    - Example:
        - `logstash-2023-10-{01..05}` matches indices `logstash-2023-10-01`, `logstash-2023-10-02`, ..., `logstash-2023-10-05`
### Index alias
_Refer to [Aliases | Elasticsearch Guide [8.15] | Elastic](https://www.elastic.co/guide/en/elasticsearch/reference/current/aliases.html)_
An Index alias is a secondary name for a group of data streams or indices. Most Elasticsearch APIs accept an alias in place of a data stream or index name.
### Index mapping
As ElasticSearch is a [[Databases#Non-relational database or NoSQL|NoSQL]] database, it allows user to store [[#Document|documents]] with any fields and any field types inside them. For example, some index may receive this data:
```JSON
{ "user": "Ringo" }
```
And suddenly might receive another document like this, with a new unexpected field `age`:
```JSON
{
	"user": "Ringo",
	"age": 45
}
```
Although ElasticSearch is NoSQL, it contains an [[#Index template]] where the fields and their datatypes are defined.
When ElasticSearch detects a new field in a document, it creates that field in the Index template.
However, we have two choices:
1. [[#Dynamic Mapping]]: Let ElasticSearch auto-detect the datatypes and create the mapping by itself
2. [[#Explicit Mapping]]: Explicitly create the mappings on our own
#### Dynamic Mapping
#### Explicit Mapping

### Index template
An index template is a way to tell Elasticsearch how to configure an index when it is created. For example, you can create a template where you specify:
- The number of **index replicas**
- The number of **shards**
- The document fields and data types (i.e. [[#Index mapping]])
- Before creating an index, you must create the template
- Index templates can be specified as [[JSON]] objects
It will typically be necessary to [[#Create an Index template]] before [[#Create a new Indices Index|creating a new index]]. When creating a template, an [[#Index pattern]] is specified to be applied to future indices that will be created matching that pattern.
#### Example
```JSON
"template": {
    "mappings": {
      "properties": {
        "@timestamp": {
          "type": "date"
        },
        "user": {
	      "type": "string"
        },
        "age": {
	      "type": "long"
        }
      }
    }
  }
```
### Reindex
_For more information, refer to [Reindex API | Elasticsearch Guide [8.15] | Elastic](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-reindex.html)_
We can copy all documents from one index to another. The destination index can be previously created, or can be created on the go.
The reindex process simplified consists of making a [[HTTP#POST]] request towards the ElasticSearc Reindex API and specify the source and destination indices:
```shell
curl -XPOST http(s)://ES_IP:ES_PORT/_reindex [other_curl_options] -H "Content-Type: application/json" -d `{"source": {"index": "<source_index>"}, "dest": {"index": "<destination_index>"}}`
```
_Note: if `<destination_index>` does not exist before running that command, it will be automatically created before the reindex process_
### ILM
_ILM stands for Index Lifecycle Management_
_Refer to [Index lifecycle management APIs | Elasticsearch Guide [8.15] | Elastic](https://www.elastic.co/guide/en/elasticsearch/reference/current/index-lifecycle-management-api.html) and [Configure a lifecycle policy | Elasticsearch Guide [8.15] | Elastic](https://www.elastic.co/guide/en/elasticsearch/reference/current/set-up-lifecycle-policy.html#apply-policy-template)_
In case you have installed ElasticSearch in a resource limited environment (with some limitations in [[Operating System#Secondary memory (Disk)|Hard disk]], for example), you can set policies to roll over the data from some index to a new one after some elapsed time, or after reaching a limit such as index space usage (recommended) or index age.
#### Rollover setup
The steps to follow will typically be:
1) Create your policy by `curl -XPUT <ES_IP>:<ES_PORT>/_ilm/policy/<policy-name>`. For example, the policy configuration can be:
```JSON
{
    "policy": {
        "phases": {
            "hot": {
                "actions": {
                    "rollover": {
                        "max_size": "15kb"
                    }
                }
            },
            "delete": {
                "min_age": "1d",
                "actions": {
                    "delete": {}
                }
            }
        }
    }
}
```
_In this example, we are instructing ElasticSearch to rollover (i.e. create) a new index when the current index we are storing data reaches 15kb size. The previous indices would keep alive, but after 1d will be deleted_
2) Two posibilities: attach the policy to an [[#Index template]] or to a specific index by attaching the policy to that [[#Index settings]]:
##### Attach policy to template
1) Create an initial index that will match the template `index_pattern` that you will create later (e.g. if you are going to create a template for `test-*` indices, in this step you should create an index like `test-000` or `test-001`)
_Note: the target index name must match the format ` _^.*-\d+$_`; for example, `my-index-00001`_
2) [[#Set an alias to an index]]. Set the following alias options: `"is_write_index": true`
3) Create an Index template and attach your policy to the recently created index template (see more details [Configure a lifecycle policy | Elasticsearch Guide [8.15] | Elastic](https://www.elastic.co/guide/en/elasticsearch/reference/current/set-up-lifecycle-policy.html#apply-policy-template)) by `curl -XPUT http(s)://<ES_IP>:<ES_PORT>/_index_template/<template-name> [other_curl_options]`

```shell
{
  "index_patterns": ["test-*"], 
  "template": {
    "settings": {
      "number_of_shards": 1,
      "number_of_replicas": 1,
      "index.lifecycle.name": "<policy-name>", 
      "index.lifecycle.rollover_alias": "<rollover-alias>" 
    }
  }
}
```
###### Example
```HTTP
### Check ElasticSearch current status
curl -XGET https://raspberrypi.local:9200 --cacert ca.crt

### Get all indices
curl -XGET https://raspberrypi.local:9200/_cat/indices

### Create/Update the policy
curl -XPUT https://raspberrypi.local:9200/_ilm/policy/test-policy \
-H "Content-Type: application/json" \
-d '
{
    "policy": {
        "phases": {
            "hot": {
                "actions": {
                    "rollover": {
                        "max_size": "15kb"
                    }
                }
            },
            "delete": {
                "min_age": "10s",
                "actions": {
                    "delete": {}
                }
            }
        }
    }
}'

### Ensure that the policy has been created
curl -XGET https://raspberrypi.local:9200/_ilm/policy/test-policy

### Create an initial index and a rollover alias at a time
curl -XPUT https://raspberrypi.local:9200/index-test-000 -H 'Content-Type: application/json' \
-d '
{
    "aliases": {
        "test_alias": {
            "is_write_index": true
        }
    }
}'

### Ensure that the alias has been correctly created
curl -XGET https://raspberrypi.local:9200/_alias/test_alias

### Ensure that the index settings are right
curl -XGET https://raspberrypi.local:9200/index-test-000/_settings

### Create a new template, attaching to it the policy
curl -XPUT https://raspberrypi.local:9200/_index_template/template-test \
-H "Content-Type: application/json" \
-d '
{
    "index_patterns": ["index-test-*"], 
    "template": {
        "settings": {
            "number_of_shards": 2,
            "number_of_replicas": 0,
            "index.lifecycle.name": "test-policy", 
            "index.lifecycle.rollover_alias": "test_alias" 
        }
    }
}'

### Ensure that the template has been created
curl -XGET https://raspberrypi.local:9200/_index_template/template-test

### Ensure that the policy is applied to the template
curl -XGET https://raspberrypi.local:9200/_ilm/policy/test-policy

### Post a document
curl -XPOST https://raspberrypi.local:9200/index-test-000/_doc -H 'Content-Type: application/json' \
-d '{"name": "Paul", "age": 54}'

### Get all documents inside the index
curl -XGET https://raspberrypi.local:9200/index-test-000/_search

### Check indices lifecycle phases
curl -XGET https://raspberrypi.local:9200/_all/_ilm/explain
```
##### Attach the policy to an index
1) [[#Set an alias to an index]]. Set the following alias options: `"is_write_index": true`
2) Apply the policy to the [[#Index settings]] by `curl -XPUT http(s)://<ES_IP>:<ES_PORT>/<indexname>/_settings [other_curl_options]`:
```JSON
{
    "index": {
        "lifecycle": {
            "name": "<policy-name>",
            "rollover_alias": "<rollover-alias>"
        }
	}
}
```
###### Example
```HTTP
### Check ElasticSearch current status
curl -XGET https://raspberrypi.local:9200 --cacert ca.crt

### Get all indices
curl -XGET https://raspberrypi.local:9200/_cat/indices

### Create/Update the policy
curl -XPUT https://raspberrypi.local:9200/_ilm/policy/test-policy \
-H "Content-Type: application/json" \
-d '
{
    "policy": {
        "phases": {
            "hot": {
                "actions": {
                    "rollover": {
                        "max_size": "15kb"
                    }
                }
            },
            "delete": {
                "min_age": "10s",
                "actions": {
                    "delete": {}
                }
            }
        }
    }
}'

### Ensure that the policy has been created
curl -XGET https://raspberrypi.local:9200/_ilm/policy/test-policy

### Create a new index and a rollover alias at a time
curl -XPUT https://raspberrypi.local:9200/index-test-0001 -H 'Content-Type: application/json' \
-d '
{
    "aliases": {
        "test_alias": {
            "is_write_index": true
        }
    }
}'

### Ensure that the alias has been correctly created
curl -XGET https://raspberrypi.local:9200/_alias/test_alias

### Ensure that the index settings are right
curl -XGET https://raspberrypi.local:9200/index-test-0001/_settings

### Attach the policy to the index, specifying the alias on the go
curl -XPUT https://raspberrypi.local:9200/index-test-0001/_settings \
-H "Content-Type: application/json" \
-d '
{
    "index": {
        "lifecycle": {
            "name": "test-policy",
            "rollover_alias": "test_alias"
        }
    }
}'

### Ensure that the policy has been attached to the index (1)
curl -XGET https://raspberrypi.local:9200/_ilm/policy/test-policy
### Ensure that the policy has been attached to the index (2)
curl -XGET https://raspberrypi.local:9200/index-test-0001/_settings

### Post a document
curl -XPOST https://raspberrypi.local:9200/index-test-0001/_doc -H 'Content-Type: application/json' \
-d '{"name": "Paul", "age": 54}'

### Get all documents inside the index
curl -XGET https://raspberrypi.local:9200/index-test-0001/_search

### Check index lifecycle phase
curl -XGET https://raspberrypi.local:9200/index-test-0001/_ilm/explain

### Check index lifecycle phase
curl -XGET https://raspberrypi.local:9200/index-test-000002/_ilm/explain

### Check indices lifecycle phases
curl -XGET https://raspberrypi.local:9200/_all/_ilm/explain
```
## Document
The data that ElasticSearch stores are called _documents_ and can be specified in [[JSON|JSON format]].
Each document has got:
- **Data**
- **Metadata** (fields which begin with `_`, such as `_index`, `_id` or `_source`).
	- `_source` is the original JSON document
	- `_index` is the [[#Indices|index]] where the document is stored in
	- `_id` is the document id
### Example
```JSON
{
  "_index": "my-first-elasticsearch-index",
  "_id": "DyFpo5EBxE8fzbb95DOa",
  "_version": 1,
  "_seq_no": 0,
  "_primary_term": 1,
  "found": true,
  "_source": {
    "email": "john@smith.com",
    "first_name": "John",
    "last_name": "Smith",
    "info": {
      "bio": "Eco-warrior and defender of the weak",
      "age": 25,
      "interests": [
        "dolphins",
        "whales"
      ]
    },
    "join_date": "2024/05/01"
  }
}
```
## Shards

## Data path
For Debian-based installations:
- [[Linux#/var|/var]]/lib/elasticsearch
You can change the data path with `paht.data` in [[#Main Configuration Files|elasticsearch.yml]]. This is very useful when you need to change the [[Operating System#Partition|partition]] where to build the database. In this case, ensure that the new directory has got the same permissions and owners as the original `/var/lib/elasticsearch`. You can create the new directory by copying `/var/lib/elasticsearch` directory and using [[UNIX#cp (Copy files)]] command with the `-p` flag.
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
##### For [[Distributions#Debian|Debian]] installation
Refer to Debian Directory Layout [Install Elasticsearch with Debian Package | Elasticsearch Guide [8.13] | Elastic](https://www.elastic.co/guide/en/elasticsearch/reference/current/deb.html#deb-layout).
You will typically find the ElasticSearch files in the following path:
```
/etc/elasticsearch
```
##### For RPM installation
Refer to RPM Installation [Install Elasticsearch with RPM | Elasticsearch Guide [8.15] | Elastic](https://www.elastic.co/guide/en/elasticsearch/reference/current/rpm.html#rpm-layout)
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
- Ensure that the permissions for `<TLS_certname.p12>` are [[GNU#chmod (Change file or directory permissions)|chmod 644]] (i.e. `-rw-r--r--`)
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
- Ensure that the permissions for `<HTTP_certname.p12>` are [[GNU#chmod (Change file or directory permissions)|chmod 644]] (i.e. `-rw-r--r--`)
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
However:
- If you are using a [[Distributions#Debian|Debian]] distirbution, it's recommended to install ElasticSearch as a [[Linux#APT#Debian package .deb|Debian package (.deb)]] (refer to [Install Elasticsearch with Debian Package | Elasticsearch Guide [8.13] | Elastic](https://www.elastic.co/guide/en/elasticsearch/reference/current/deb.html)), since the configuration files will be automatically placed where they should be. Remember the [[Linux#APT|steps]] for installing Debian packages
	- In this case, two options are available: 1) installing through the repository (in this case you will need to import the GPG key first [import the GPG key first](https://www.elastic.co/guide/en/elasticsearch/reference/current/deb.html#deb-key) and then configure the repository in `/etc/apt/sources.list.d`) or 2) manually installing the `.deb` package
- On condition that you are using a distribution with [[Linux#RPM|RPM]] as package manager tool (such as [[Distributions#Fedora|Fedora]] or [[Distributions#Red Hat Enterprise Linux (RHEL)|RHEL]]), you can download the [[Linux#RPM file|.rpm package]] and [[Linux#DNF#Install `.rpm` package|install it]]. Refer to [Install Elasticsearch with RPM | Elasticsearch Guide [8.15] | Elastic](https://www.elastic.co/guide/en/elasticsearch/reference/current/rpm.html)
### Configure ElasticSearch
Go to [[#Configuration Files Path]].
There are some [[#Important configurations for a ElasticSearch Node]] you can do before deploying ElasticSearch.
### Run ElasticSearch = Run one ElasticSearch node
Depending on how you installed ElasticSearch:
- If you installed it as a binary file, [[BinaryFile#How to run|Run elasticsearch binary file]]. After running it, it will take the configuration from ```/config``` directory inside the [[#Download ElasticSearch|extracted folder]]
- If you installed it as a service (with Debian or RPM Package), [[Linux Service#Start service|start elasticsearch.service]]. You might need to [[Linux Service#Daemon-reload|damon-reload]] and then [[Linux Service#Enable a service|enable .service]] to force changes to take effect. If you find some error logs related to ElasticSearch upgrade:

	_org.elasticsearch.gateway.CorruptStateException: Format version is not supported. Upgrading to [8.12.2] is only supported from version [7.17.0]_

	You will need to [[UNIX#rm (Remove files or directories)|remove]] the [[#Data path|elasticsearch data]], [[Linux#APT#Uninstall package|purge the elasticsearch package]] and [[Linux#APT#Install a Debian package|install elasticsearch again]]
_Remember that you can see Linux Service logs ([[Linux Service#See service logs|See service logs]])_
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
See [[#Configuration Files Path]] (type: bin) to know where this tool is located.
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
The certificate will be typically placed under `..` (i.e. the previous directory of `elasticsearch-certutil` binary). 
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
##### Remove pasword from keystore
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
## ElasticSearch HTTP requests
_In this section, we use [[Useful commands#curl|curl]] command to send requests to the ElasticSearch instance_
### Get all [[#Indices]]
Retrieve ElasticSearch [[#Indices]]:
```shell
curl -X GET http(s)://ES_IP:ES_PORT/_cat/indices [other_curl_options]
```
### Create a new [[#Indices|Index]]
```shell
curl -X PUT http(s)://ES_IP:ES_PORT/<indexname> [other_curl_options]
```
### Get [[#Index settings]]
```shell
curl -X GET http(s)://ES_IP:ES_PORT/<indexname>/_settings [other_curl_options]
```
### Set an alias to an index
If the index already exists:
```shell
curl -X POST http(s)://ES_IP:ES_PORT/_aliases [other_curl_options] -H "Content-type: application/json" -d '
{
  "actions": [
    {
      "add": {
        "index": "<indexname>",
        "alias": "<alias>",
        ... (alias options)
      }
    }
  ]
}'
```
If the index does not exist yet, you can [[#Create a new Indices Index|create it]] and set the alias at a time:
```shell
curl -X PUT http(s)://ES_IP:ES_PORT/<indexname> [other_curl_options] -H "Content-type: application/json" -d '
{
  "aliases": {
    "<alias_name>":{
      ... (alias options)
    }
  }
}'
```
### Get an [[#Index alias|alias]]
```shell
curl -X GET http(s)://ES_IP:ES_PORT/_alias/<aliasname> [other_curl_options]
```
### Get all [[#Index alias|aliases]]
```shell
curl -X GET http(s)://ES_IP:ES_PORT/_aliases [other_curl_options]
```
### Update [[#Index settings]]
```shell
curl -X PUT http(s)://ES_IP:ES_PORT/_settings [other_curl_options] -H 'Content-type: application/json' -d '
{
	...
}
'
```
If the index does not exist yet, you can specify the settings when [[#Create a new Indices Index|creating a new index]] as:
```shell
curl -X PUT http(s)://ES_IP:ES_PORT/<indexname> [other_curl_options] -H 'Content-type: application/json' -d '
{
  "settings": {
    ...
  }
}'
```
### Add a new [[#Document]] to an [[#Indices|Index]]
_Refer to [Index API | Elasticsearch Guide [8.15] | Elastic](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-index_.html)_
```shell
curl -X POST http(s)://ES_IP:ES_PORT/<indexname>/_doc [other_curl_options] -H 'Content-Type: application/json' -d '{...(document)...}'
```
You can use the Bulk API ([Bulk API | Elasticsearch Guide [8.15] | Elastic](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-bulk.html)) to insert several documents at a time.
If want to specify the new document id, use [[HTTP#PUT]] and `/<indexname>/_doc/<id>` endpoint.
### Get data [[#Document|Documents]] inside an [[#Indices|Index]]
```shell
curl -X GET http(s)://ES_IP:ES_PORT/<index>/_search?pretty [other_curl_options]
```
If want to query documents:
#### Query data
_Refer to [The search API | Elasticsearch Guide [8.13] | Elastic](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-your-data.html)_
```shell
curl -X GET http(s)://ES_IP:ES_PORT/<index>/_search [other_curl_options]
{
	<QueryDSL-Body>
}
```
Queries can be performed in two contexts:
- **Query context**: We write the query and ElasticSearch will calculate how well each document matches the query, sorted in descending order by some score value
- **Filter context**: We write the query and ElasticSearch will return only the documents that fulfill the query
##### Query DSL
_Refer to [Query DSL | Elasticsearch Guide [8.13] | Elastic](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl.html)_
The query is [[DSL]] and it is based on [[JSON]].
###### Get the documents which have some fieldname
```JSON
{"query": {"exists": {"field": "<fieldname>"}}}
```
So as to display only certain fields:
```JSON
{"query": {"exists": {"field": "<fieldname>"}}, "_source": ["<fieldname1>", "<fieldname2>", ...]}
```
But if you want to display a [[#Runtime fields|runtime field]], then:
```JSON
{"query": {"exists": {"field": "<fieldname>"}}, "fields": ["<runtime_fieldname1>", "<runtime_fieldname2>", ...]}
```
###### Get the documents which do not contain some fieldname
```JSON
{"query": {"bool": {"must_not": {"exists": {"field": "<fieldname>"}}}}}
```
###### Get the last timestamp
_Refer to [[#Handy commands]]_
```JSON
{"size": 1, "sort": [{"timestamp_field": {"order": "desc"}}]}
```
###### Select distinct values of some field
```JSON
{"aggs": {"whatever": {"terms": {"field": <fieldname>}}}, "size": 0}
```
###### Test [[#Painless Scripting|Painless Script]]
```shell
curl -XGET http(s)://ES_IP:ES_PORT/<index_name>/_search?pretty [other_opts] \
-H "Content-Type: application/json" \
-d '
{
  "runtime_mappings": {
    "<runtime_fieldname>": {
      "type": "<output_datatype_of_painless_script>",
      "script": "<Painless_Script>"
    }
  },
  "query": {
    "match_all": {}
  },
  "fields": ["<runtime_fieldname>"] <--- This is necessary to show the runtime field in the output
}
'
```
The `<output_datatype_of_painless_script>` cannot be `int`: only `double` is supported in this case ([Map a runtime field | Elasticsearch Guide [8.15] | Elastic](https://www.elastic.co/guide/en/elasticsearch/reference/8.15/runtime-mapping-fields.html)).
### Create / Update an Index [[#ILM]] policy
```shell
curl -X PUT http(s)://ES_IP:ES_PORT/_ilm/policy/<policy-name> [other_curl_options] -H "Content-Type: application/json" -d '
{
	... (Policy configuration)
}'
```
### Get an [[#ILM]] policy
```shell
curl -X GET http(s)://ES_IP:ES_PORT/_ilm/policy/<policy-name> [other_curl_options]
```
### Enforce an index to rollover
```shell
curl -X POST http(s)://ES_IP:ES_PORT/<rollover_alias_name>/_rollover [other_curl_options]
```
### Troubleshoot [[#ILM]] execution issues and see current Indices Lifecycle Phases
```shell
curl -X GET http(s)://ES_IP:ES_PORT/_all/_ilm/explain [other_curl_options]
```
_Note: for troubleshooting specific index ILM executions, you can spcify it with `<indexname>` instead of `_all`_
### Check index health
You can both [[#5. Check cluster health|check ES cluster health]] and check the health for a specific index with:
```shell
curl -X GET http(s)://ES_IP:ES_PORT/_cluster/health/<index> [other_curl_options]
```
### Delete index
```shell
curl -X DELETE http(s)://ES_IP:ES_PORT/<index> [other_curl_options]
```
### Delete all documents inside some index
Go to [[Kibana]] and click on _Management_. Then, go to _Data > Index Management_, select your index and in _Manage index_ select _Flush index_.
### Create an [[#Index template]]
```shell
curl -X PUT http(s)://ES_IP:ES_PORT/_index_template/<template_name> [other_curl_options] -H 'Content-type: application/json' -d '
{
	"index_patterns": ["<index_pattern (e.g. metrics-*)>"],
	"template": {
		"settings": {
			"number_of_shards": 1,
			"number_of_replicas": 1
		}
	}
}
'
```
### Get all [[#Index template|index templates]]
```shell
curl -X GET http(s)://ES_IP:ES_PORT/_index_template [other_curl_options]
```
### Get [[#Index template]]
```shell
curl -X GET http(s)://ES_IP:ES_PORT/_index_template/<template> [other_curl_options]
```
### Get [[#Index mapping]]
```shell
curl -X GET http(s)://ES_IP:ES_PORT/<index>/_mapping [other_curl_options]
```
#### Kibana Discover App
_Refer to [[Kibana#Explore ElasticSearch data]]_
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
- Get `@timestamp` format: your database stores timestamped data with a `@timestamp` field. When you visualize data (e.g. from [[#Kibana Discover App]]), timestamps might be converted to your local timezone date (taken from your web browser), so there is no way to know what the real raw timestamp is. You can use this [[#Query DSL]] to retrieve the raw timestamp value:
```shell
curl -X GET http(s)://ES_IP:ES_PORT/<index>/_search -H "Content-type: application/json" -d '{"size": 1, "sort": [{"timestamp_field": {"order": "desc"}}]}' [other_curl_options]
```
### Index notation
- If the index begins with `.`, it is a self-managed index (managed by ElasticSearch itself).
- `ds = data stream`
## Painless Scripting
_Refer to [[Painless]]_
### Runtime fields
You can create a new field which is a transformation (e.g. a sum or substract) of another fields in a document. The transformation is defined through a Script, written in [[Painless]].
For this purpose, two options are available:
1. Configure the [[#Index mapping]] and add the runtime field Script
2. From [[Kibana]]: go to _Management > Data Views_, select a Data View, which is actually an [[#Index pattern]], and get into _Add Field_. Check _Set value_ option and define the [[Painless]] Script
_Note: in case you have added in the runtime field by the second way (Kibana), that runtime field will be only available inside Kibana and only for data visualizations, not for any use case such as [[Grafana]]. The recommended way is the first one, that permanently creates the runtime field as though it were another generic field_
If want to try a Painless Script, you can perform a [[#Test Painless Scripting Painless Script]].
# References
- [Elastic Products - Entry Point of Documentation](https://www.elastic.co/guide/index.html)