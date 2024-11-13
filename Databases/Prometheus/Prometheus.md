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
Alerting is done via Alertmanager. It comes with prometheus.
### Alertmanager
_It is an [[Internet#Endpoint|endpoint]] where we will send alerts as [[JSON]] objects_
It allows to handle alerts from different sources. For example, here is where you will define...
- Whom to send those alerts when firing depending on the alert field values (e.g. alerts with `internal==True` will be sent to mail `stuff@enterprise.com` and alerts with `internal==False` will be sent to `stuff@enterprise.com` and `client_stuff@enterprise.com`)
- **Grouping**: if several alerts are firing for the same host (for instance, see the JSON objects below representing different firing alerts), you can group them by `hostname` and, instead of sending 3 mails (one for each alert), you can send just 1 mail containing the three alerts inside
```JSON
[{
  "labels": {
    "alertname": "CPU usage",
    "hostname": "my_server"
  },
  "annotations": {
	"dashboard": "https://my_dashboardA"
  }
  "startsAt": "2024-11-05T10:02:40.544069-06:00",
  "endsAt": "2024-12-05T10:02:40.544069-06:00"
},
{
  "labels": {
    "alertname": "Disk usage",
    "hostname": "my_server"
  },
  "annotations": {
	"dashboard": "https://my_dashboardA"
  }
  "startsAt": "2024-11-05T10:02:40.544069-06:00",
  "endsAt": "2024-12-05T10:02:40.544069-06:00"
},
{
  "labels": {
    "alertname": "RAM usage",
    "hostname": "my_server"
  },
  "annotations": {
	"dashboard": "https://my_dashboardB"
  }
  "startsAt": "2024-11-05T10:02:40.544069-06:00",
  "endsAt": "2024-12-05T10:02:40.544069-06:00"
}]
```
- **Silencing**: you can silence some alerts depending on some alert values
#### alertmanager.yml
_The configuration file for Alertmanager_
##### For [[Linux]] installations
Alertmanager configuration can be typically made through `/etc/prometheus/alertmanager.yml`. Nonetheless, the configuration file can be specified when running Alertmanager like `./alertmanager --config.file=alertmanager.yml`. Alertmanager will be typically installed as a [[Linux Service]] together with Prometheus, and consequently you can configure in the [[Linux Service#Unit file|Unit file]] the configuration file path in case you want to place `alertmanager.yml` in another location different from `/etc/prometheus/`.
##### For [[Docker]] installations
We can install Alertmanager standalone with [[Docker]] (see [[#Test Environment]]), i.e. without Prometheus. In this case, we will need to specify the configuration file path when starting the container [[Docker#Template example|see command: option in docker-compose.yml]].
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

- **Server**: scrapes and stores data from [[#Job|jobs]] (or [[#Exporter|exporters]])
- **Pushgateway**: only necessary for short-lived jobs
- **[[#Alerting operation#Alertmanager|Alertmanager]]**: handles alerts
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
See [[Linux#/etc]].
### Prometheus as Linux Service
_Refer to [Prometheus First Steps](https://prometheus.io/docs/introduction/first_steps/)_
#### Download Prometheus
_Refer to [Prometheus Downloads](https://prometheus.io/download/)_
Download the `.tar.gz` file for your [[Operating System]] and your [[Operating System#CPU Architectures|CPU Architecture]].
Then, [[GNU#tar (tape archive)#Extracting files|extract it]].
## How to install an exporter
_Refer to [Prometheus Exporters](https://prometheus.io/docs/instrumenting/exporters/)_
Most of them are [[GitHub]] repositories. You will typically have to look for [[Bash Scripting|binaries]] link in `README.md` and then [[Linux Service#Daemonize Bash command|turn it into a service]].

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


### alertmanager.yml
_Refer to [Alerting configuration](https://prometheus.io/docs/alerting/latest/configuration/)_
#### templates:
In case we want to apply a custom design for mail notifications, we will need to use this configuration.
```YAML
templates:
	- '/path/to/my_template.tmpl'
```
Where `/path/to/my_template.tmpl` is the path to the template to use (`.tmpl` is the extension for [[Go#Packages#Template|Go Template]]). As Go Template Package uses a data structure to create the template, in this case the data structure from which the template will be created is [Prometheus Alerts Data Structure](https://prometheus.io/docs/alerting/latest/notifications/).
_How it works? when [[#Alertmanager]] receives JSON alerts, those alerts will pass through the `my_template.tmpl` file. The data struture that the Go template will receive will represent the alerts firing currently._
In this case, an additional step is needed to render the template. The defined template in `/path/to/my_template.tmpl` won't be shown iself, but a subtemplate defined there. You must specify it in [[#receivers]] section (see `html` option under `email_configs:` option). That's why we have to define a subtemplate in [[Go#Packages#Template|Go Template]] using the [[Go#Packages#Template#Actions|{{ define... }}]] action
#### receivers:
Where we define who to send the alert notifications to. For example:
```YAML
receivers:
	- name: me
	  email_configs:
		  - to: 'mymail@gmail.com'
			html: '{{ template "<my_template>" . }}'
```
#### Test Environment
_Refer to [Alertmanager Test Environment](https://github.com/mg-Ben/alertmanager-testEnvironment)_
## PromQL queries
Supposing you have these metrics:
```
temperature{cpuIndex="1", cpu="Intel", machine="server1", ...} 81
temperature{cpuIndex="2", cpu="Intel", machine="server2", ...} 57
temperature{cpuIndex="3", cpu="Intel", machine="database1", ...} 47
temperature{cpuIndex="4", cpu="AMD", machine="database2", ...} 78
temperature{cpuIndex="5", cpu="AMD", machine="database3", ...} 80
processes{cpuIndex="1", ...} 34
processes{cpuIndex="2", ...} 5
processes{cpuIndex="3", ...} 380
processes{cpuIndex="4", ...} 340
processes{cpuIndex="5", ...} 40
...
```
### Point by point operation
Supposing you want to perform some operation between two or more metric values for each time sample, those metrics must have the same labels.
### Remove labels from query result
If you don't want to show all labels for the query result, use `without` clause:
```
(PromQL_query) without (label1, label2, label3...)
```