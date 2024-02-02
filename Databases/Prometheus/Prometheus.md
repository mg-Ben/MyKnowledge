---
tags:
  - Databases
---
# Core principles
## Definition
Monitoring and Alerting toolkit and Open-Source. It serves for collecting **time series data** by **scraping** data from some **instance**. To do so, It sometimes uses an intermediary **Pushgateway**, but not always.
Prometheus also labels time series data (e.g., we can monitor ```server: A, instance: logstash```, ```server: A, instance: elasticsearch```, ```server: B, instance: Operative System Status```...). This is called **multidimensionality**.
## Scraping operation
Prometheus retrieves data from an endpoint by requesting that data periodically. For each requesting sample, Prometheus waits a timeout to receive that sample.
## Alerting operation

## Terms
### Metric
It refers to time serie (e.g., metric "temperature", "requests", "responses"...). It has got labels. The format is like:
```temperature{server: A, instance: logstash}```, this is:
```<metric_name>{label1: A, label2: B...}```
### Instance
It means target process to be scraped. They are typically HTTP endpoints.
### Job
A group of [[#Instance|instances]].
### Exporter
A target process to be scraped (i.e., an [[#Instance|instance]]). However, this process is often made by developers (e.g., in GitHub) to make them adaptable for Prometheus. E.g. the [[SNMP Exporter]] is a [[Bash Scripting]] Script which has been custom-developed so that Prometheus can scrape data from it.
## Components
Components are mostly written in [[Go]].

![[Prometheus-components.png]]

- **Server**: scrapes and stores data.
- **Pushgateway**: only necessary for short-lived jobs.
- **Alertmanager**: handles alerts.
# Hands on
## How to deploy
You can deploy your Prometheus Server on a [[Docker#Docker container]] or as a [[Linux Service]].
The basic steps to make your Prometheus server run are:
1. Configure your ```prometheus.yml``` file (see [[#prometheus.yml]])
2. Start Prometheus Server
### Prometheus as Docker container
[[Docker#3. The reality docker-compose.yml|Run a Prometheus container]] with base-image ```prom/prometheus``` (see [Prometheus image in DockerHub](https://hub.docker.com/r/prom/prometheus/)). Create your own ```prometheus.yml``` configuration file wherever you want on your hostOS machine and [[Docker#Docker volume#Bind-mount approach|bind-mount it]] into ```/etc/prometheus``` inside the container.
The ```docker-compose.yml``` would look like:
```yaml
version: "0.1"
services:
	prometheus:
		container_name: prom-container
		image: prom/prometheus:${PROMETHEUS_VERSION}
	volumes:
		- prom-data:/prometheus
		- <path_to_prometheus_yml>:/etc/prometheus #Bind mount prometheus.yml
```
Then, ```docker compose up```.
### Prometheus as Linux Service
## Configuration files
### prometheus.yml
The file where we define the [[#Job|jobs]] to scrape, as well as the scrape parameters, the target instances, the metrics labels... [There are lots of parameters](https://prometheus.io/docs/prometheus/latest/configuration/configuration/) to configure here, but here we show the main of them.
At first sight, the ```prometheus.yml``` file consists of different sections that are explained below.
#### Global configuration
Configuration for all the jobs (scraped processes) of prometheus. You can configure:
- [[#Scraping operation|Sampling period]], called ```scrape_interval```
- [[#Scraping operation|Timeout]], called ```scrape_timeout```
- [[#Alerting operation|Evaluation period]], called ```evaluation_interval```
- ```external_labels```: this means that, when scraping an external system, the time series will have these labels (e.g.: ```temperature{machine: "external-test-servers"}```)
Code example:
```yaml
global:
	scrape_interval: 10s
	scrape_timeout: 9s #Typically we can choose = scrape_interval - 1s
	external_labels:
		machine: external-test-servers
	evaluation_interval: 10s
```
#### Scrape configuration
Here you define and configure the jobs, i.e. the endpoints to scrape.
##### Job configuration
- ```job_name```: the job names
-  [[#Scraping operation|Sampling period]], called ```scrape_interval``` specifically for each job. This configuration would override the [[#Global configuration]] value.
- [[#Scraping operation|Timeout]], called ```scrape_timeout``` specifically for this job. This configuration would override the [[#Global configuration]] value.
- [[#Relabel configs]]
###### Static configs: targets and labels
Here is where you set the target endpoint list to scrape for the job. You can configure:
- ```targets``` list. You can either specify them on the same line like ```['1.2.3.4:1002', 'localhost:8000', '45.44.43.32:8090'...]``` or like a list:
```yaml
- targets:
	- '1.2.3.4:1002'
	- 'localhost:8000'
	- '45.44.43.32:8090'
```
- Labels to attach to the [[#Terms#Metric|metrics]] to all those targets (```labels```)
Example code:
```yaml
scrape_configs:
	- job_name: prom_job
	  scrape_interval: 5s
	  scrape_timeout: 4s
	  static_configs:
		  - targets: ['1.2.3.4:1002', 'localhost:8000', '45.44.43.32:8090']
		    labels:
			    mylabel: 'value'
```
###### File Service Discovery configs: targets and labels
In this case, you can make your targets and labels settings on a separated file (either ```.yml``` or ```.JSON```) such as:
```JSON
[
	{
		'targets': [
			'1.2.3.4:1002',
			'localhost:8000',
			'45.44.43.32:8090'
		],
		'labels': {
			'mylabel': 'value'
		}
	}
]
```
Or:
```yaml
- targets:
	- '1.2.3.4:1002'
	- 'localhost:8000'
	- '45.44.43.32:8090'
	labels:
		mylabel: 'value'
```
Then, reference to it inside ```prometheus.yml```:
```yaml
scrape_configs:
	- job_name: prom_job
	  scrape_interval: 5s
	  file_sd_configs:
	  - files:
		- <path_to_yaml_or_json_file>
```
###### Important: format of targets endpoints
The ```target``` value both for [[#Static configs targets and labels]] and [[#File Service Discovery configs targets and labels]] must have the format ```IP:port``` (e.g. ```localhost:8080```). However, how can we scrape endpoints with _Request path_ or _Query string_? (see [[HTTP]]).
###### Scraping endpoints with Request path and Query string
You have two options:
- **Easy way**: add ```metrics_path``` and query string ```params``` to the [[#Scrape configuration]]. **The problem**: all the targets might not have the same ```metrics_path``` and query string ```params``` or values. Example: _This example would scrape 1.2.3.4:1002/users?var1=X, localhost:8000/users?var1=X and 45.44.43.32:8090/users?var1=X_, but we cannot scrape anything different from _<IP:port>**/users?var1=X**_:
```yaml
scrape_configs:
	- job_name: prom_job
	  scrape_interval: 5s
	  scrape_timeout: 4s
	  metrics_path: '/users'
	  params:
		  var1: X
	  static_configs:
		  - targets: ['1.2.3.4:1002', 'localhost:8000', '45.44.43.32:8090']
		    labels:
			    mylabel: 'value'
```
- **Good and difficult way**: set the targets as you want, with a different format from ```IP:port```, but later use ```relabel_configs``` (see [[#Relabel configs]]).
###### Relabel configs
Relabel configs serve for two main use cases:
- ```N-to-1```: Depending on ```IP:port```, we concatenate a fixed ```metrics_path``` and a fixed query string ```params``` and fixed values for those query string ```params```. For example:
	- When connecting to ```1.2.3.4:8080```, concatenate ```/users?username=Bob&userage=17``` to the URL. To do so, inside ```relabel_configs``` section you have to set the [[#Regular expressions|regular expression]] to check whether ```IP:port=1.2.3.4:8080``` and, if true, you have to store into ```__metrics_path__``` reserved word the _Request path_ (```/users``` in the example) and you have to store into ```__param_<paramname>``` reserved word the values of the _Query string_ (in the example, ```__param_username = Bob``` and ```__param_userage = 17```).
	- When connecting to ```9.8.7.6```, no matter the port, concatenate ```/flats?height=1.68```. Here we are attaching the same _Request Path_ and _Query string_ to several ```IP:port``` tuples. To do so, the same: play around reserved keywords. In this case, the [[#Regular expressions|regular expression]] changes.
	- When conecting to ```1.60```, no matter the following IP numbers, concatenate ```/cars?color=red&weight=1.3```. Here we are attaching the same _Request Path_ and _Query string_ to several ```IP:port``` tuples. To do so, the same. See the example below.
```yaml
scrape_configs:
	- job_name: prom_job
	  scrape_interval: 5s
	  scrape_timeout: 4s
	  static_configs:
		  - targets: ['1.2.3.4:8080', '1.2.3.4:8085', '1.2.3.4:8090', '9.8.7.6:4055', '9.8.7.6:800', '1.60.15.15:0002'...]
	  relabel_configs:
	  - source_labels: [__address__] #Take the target IP:port value (e.g. 1.2.3.4:8090)
		regex: 'regex to check whether the IP:port is 1.2.3.4:8080'
		target_label: __metrics_path__ #If true, add a metrics_path label (the variable name must be __metrics_path__)
		replacement: ['users'] #And the metrics_path value will be = users
	  - source_labels: [__address__] #Take the target IP:port value (e.g. 1.2.3.4:8090)
		regex: 'regex to check whether the IP:port is 1.2.3.4:8080'
		target_label: __param_username #If true, add a query string param whose name is username (generically, __param_<param-name>)
		replacement: ['Bob'] #And the query string param value will be = Bob
	  - source_labels: [__address__] #Take the target IP:port value (e.g. 1.2.3.4:8090)
		regex: 'regex to check whether the IP:port is 1.2.3.4:8080'
		target_label: __param_userage #If true, add a query string param whose name is userage (generically, __param_<param-name>)
		replacement: ['17'] #And the query string param value will be = 17
```
- ```1-to-N```: For the same ```IP:port```, we can define different ```metrics_path```, query string ```params``` and query string values. This is the most general and recommended use case, as you can write the target URL in the format you want and even with query strings. In this case, although you have the freedom to write any URL target, you must save into reserved keyword ```__address__``` the ```IP:port``` so that Prometheus accepts the target (i.e. Prometheus accepts any target URL as long as you later assign the right ```IP:port``` to ```__address__``` variable). For this purpose, you need to create the right [[#Regular expressions|regular expression]] to take the ```IP:port``` part of the entire URL, as well as the regexp to take the _Request path_ to save into ```__metrics_path__``` and the _Query string params_ to save into each ```__param_<paramname>__```. See the example below.
```yaml
scrape_configs:
	- job_name: prom_job
	  scrape_interval: 5s
	  scrape_timeout: 4s
	  static_configs:
		  - targets: ['localhost:8080/users?username=Bob&userage=17']
	  relabel_configs:
	  - source_labels: [__address__] #Take the target value (e.g. localhost:8080/users?username=Bob&userage=17)
		regex: 'regex to take /users part of the target URL string'
		target_label: __metrics_path__ #Store that part in __metrics_path__ keyword
	  - source_labels: [__address__] #Take the target value (e.g. localhost:8080/users?username=Bob&userage=17)
		regex: 'regex to take Bob part of the target URL string'
		target_label: __param_username  #Store that part in __param_username keyword
	  - source_labels: [__address__] #Take the target value (e.g. localhost:8080/users?username=Bob&userage=17)
		regex: 'regex to take 17 part of the target URL string'
		target_label: __param_userage #Store that part in __param_userage keyword
	  - source_labels: [__address__] #Take the target value (e.g. localhost:8080/users?username=Bob&userage=17)
		regex: 'regex to take IP:port part of the target URL string'
		target_label: __address__ #Overwrite __address__ with IP:port. Now Prometheus will work!
```
###### Regular expressions
An important issue is that the [[Regular Expressions]] syntax we define inside ```prometheus.yml``` is Golang.
Another important issue is that the regular expression must match at level 1 (see [[Regular Expressions#Matching Groups]]) to take effect.
#### Rules configuration
#### Alerting configuration

