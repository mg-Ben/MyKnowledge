---
tags:
  - Others
  - Elastic
  - Ingest_add_your_data
---
# Core principles
## Logstash configuration
### Configurations files location
#### Linux local installations
- `/etc/logstash/`
## Pipelines
They are `.conf` files where the pipeline processing is defined.
### Pipeline Scripts location
#### Linux local installations
- `/etc/logstash/conf.d`

# Hands on
## Create a pipeline
Go to [[#Pipeline Scripts location]] and create your `.conf` file.
## Configure a pipeline
### Run Logstash Server for Elastic Agents
#### 1. Configure Fleet
Go to Fleet Settings in Kibana (Kibana > Management > Fleet > Settings > Output) and configure a new output with type Logstash.
An encryption key will be needed in this step. You will need to [[Kibana#Configuration Files#Main Configuration Files|configure kibana.yml file]] and add this line:
```kibana.yml
xpack.encryptedSavedObjects:
	encryptionKey: "min-32-byte-long-strong-encryption.key"
```
#### 2. Configure security between Elastic Agents and Logstash Server
You will need to [[ElasticSearch#Download ElasticSearch|Download ElasticSearch]]. Then, run [[ElasticSearch#elasticsearch-certutil|elasticsearch-certutil]]:

1. [[ElasticSearch#Generate Certification Authority|Generate a Certification Authority]] for logstash (e.g. `logstash-ca.p12)
2. Generate two certificates:
	1. One for **logstash** itself: use the [[ElasticSearch#elasticsearch-certutil#Generate the Transport-Layer certificate for the node|Generate the TLS/SSL certificate]] specifying as CA the `logstash-ca.p12` file. In this particular case, you must set the `--dns`.
	   - After creating the certificate for logstash, use [[SSL-TLS#Convert from .key to .pkcs8.key]] command to convert it to `.pkcs8.key` as it will be used later. _Remember that this TLS/SSL certificate will be sent to others (others = Elastic Agents in this case)_
	   - To make the Elastic Agents trust our Logstash CA, go to Kibana > Fleet and in Logstash Output Settings enter the CA content in *Server SSL certificate authorities (optional)* section
	   - In this case, Logstash Server must also trust in Elastic Agents! Therefore, Elastic Agents must send a certificate to Logstash. For this purpose, we will need to generate a certificate for Fleet
	2. The other one will be for **Fleet**: use the [[ElasticSearch#elasticsearch-certutil#Generate the Transport-Layer certificate for the node|Generate the TLS/SSL certificate]] specifying as CA the `logstash-ca.p12` file:
	   - Then, go to Fleet > Settings and add the `.crt` and `.key` content in **Client SSL certificate** and **Client SSL certificate key** sections
	   - To make the Logstash trust Elastic Agents, in the `pipeline.conf`, set `ssl_certificate_authorities` to the Logstash CA `.crt` file

Configure the following input in your `.conf`:
```.conf
input {
	elastic_agent {
		port => 5044 # This is the Logstash server listening port
		enrich => none
		ssl_enabled => true
		ssl_certificate_authorities => ["<path/to/logstash/CA.crt>"] #To trust the 
		ssl_certificate => "<path/to/logstash/SSL-TLS.crt>"
		ssl_key => "<path/to/logstash/SSL-TLS.pkcs8.key>"
	}
}
```
### Run Logstash Server for Elastic Beats
Configure the following input in your `.conf`:
```
input {
	beats {
		port => 5044 # This is the Logstash server listening port
	}
}
```
### Configure security between Logstash and ElasticSearch
In case ElasticSearch is configured with security (i.e. you need to access through HTTP**S**), you must make Logstash trust [[ElasticSearch#4. Security|ElasticSearch Certificate Authority]] in `output` section specifying the path of the `elasticsearch-CA.crt`:
```.conf
output {
	elasticsearch {
		hosts => ["https://<ES_IP>:<ES_PORT>"]
		cacert => '<path/to/elasticsearch-ca.crt>'
	}
}
```