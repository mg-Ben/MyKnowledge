---
tags:
  - Others
  - Elastic
  - Ingest_add_your_data
---
# Core principles
## Terms
### Pipeline
It is the Script that processes the input data. See the [[#Create a pipeline#Example template]]. It contains several [[#Plugin|plugins]] inside.
A pipeline always consists of three main plugins:
- The [[#input{}]] plugin
- The [[#filter{}]] plugin
- The [[#output{}]] plugin
Inside `ìnput{}` and `output{}` plugins, we can also use the [[#codec => ... {}]] plugin.
#### Plugin
We denote as plugin each section in a [[#Pipeline]].
##### input{}
_To see where you can take data from, refer to [available input{} plugins](https://www.elastic.co/guide/en/logstash/current/input-plugins.html)_
Here we will define where to take data from. There can be more than one input.
##### filter{}
_To see the available operations you can perform to your input data, refer to [available filter{} plugins](https://www.elastic.co/guide/en/logstash/current/filter-plugins.html)_
Here we will define how to process the data.
###### aggregate{}
_Refer to [Aggregate filter plugin | Logstash Reference [8.13] | Elastic](https://www.elastic.co/guide/en/logstash/current/plugins-filters-aggregate.html)_

> [!NOTE] **Before start...**
> _You should be very careful to [[#pipeline.workers|set Logstash filter workers to 1]] (`-w 1` flag) for this filter to work correctly otherwise events may be processed out of sequence and unexpected results will occur._

Imagine that your incoming logs have a **common field value**. You can aggregate them into a single one as they are arriving. The operation to do with all the logs belonging to the same group is defined inside `code => ""` section. Generically, the `aggregate{}` plugin looks like this:
```JSON
filter{
	aggregate{
		task_id => "%{fieldName}"
		code => "
			#Code-to-run
		"
		#Other-options
	}
}
```
Whenever a new log comes, this code will examine the `fieldName` value. Suppose that `fieldName` is, for example, `processId`. In that case, suppose that this is the first log we receive, with `processId=773`:
```JSON
log1: {..., "processId":773, ...}
```
Therefore, there is no any group yet, so this code would create the first group: _all logs with `processId=773`_. Then, it creates a variable called `map`, that is a global variable shared in the same group `processId=773`, and then it runs the code.
- That `map` variable serves for declaring global variables along the same group (e.g. `map['sum_count']` would declare a shared variable `sum_count` in that group). For instance, if we want to count the number of logs with `processId=773`, we will need to access to `sum_count` when a new log with `processId=773` comes and then sum to that variable +1.
Now, suppose that another log arrives, with `processId=773` too:
```JSON
log1: {..., "processId":773, ...}
log2: {..., "processId":773, ...}
```
Therefore, that log would belong to the same group as the first log. Then, we load the `map` shared variable for that group and we can use it to perform something like adding `+1` to the current value.

As logs can arrive until the end of the times and we don't know if at some point in the time will arrive another log with `processId = 773`, the code could be waiting indefinitely, so we must set a condition to stop aggregating.
- That condition can be a `timeout` for example
```JSON
filter{
	aggregate{
		task_id => "%{fieldName}"
		code => "
			#Code-to-run
		"
		timeout => 60 # Set a timeout of 60 seconds
		timeout_code => "" # (Optional): code to run when timeout triggers
	}
}
```
- Or we can even do something better; for instance, we can do that, if the `fieldName` changes its value, stop aggregating:
```JSON
log1: {"timestamp": 12345, "message": "[INFO]: Everything OK"}
log2: {"timestamp": 12345, "message": "[INFO]: Connecting to ..."}
log3: {"timestamp": 12348, "message": "[INFO]: Everything OK"}
```
For example, in this case, we know that the timestamp with value `timestamp = 12345` won't appear again, as timestamp it is an increasing value. Therefore, we can aggregate logs until `timestamp` changes its value with `push_previous_map_as_event => true` option:
```JSON
filter{
	aggregate{
		task_id => "%{fieldName}"
		code => "
			#Code-to-run
		"
		push_previous_map_as_event => true
	}
}
```
Note that the `code =>` runs for each incoming log. If this is the first log of the group (i.e. the first group), we would have to initialize our variables, but if this is not the first log for the group, we shouldn't initialize the variables or we will overwrite the existing ones. To do so, we can use the `||=` operator, which tells logstash: _Initialize this variable if not initialized, or don't initialize it if it was initialized before_:
```JSON
code => "
	map['count_logs'] ||= 0
	map['count_logs'] += 1
"
```
In this case, for example, if we have these incoming logs:
```JSON
log1: {"timestamp": 12345, "message": "[INFO]: Everything OK"}
log2: {"timestamp": 12345, "message": "[INFO]: Connecting to ..."}
log3: {"timestamp": 12348, "message": "[INFO]: Everything OK"}
```
This is how it works:
1. `log1` arrives: we examine if there is a group for `timestamp=12345`. There isn't a group as this is the first log. We create a group and its `map` shared variable. We run `code => `. We see that `map['count_logs']` variable doesn't exist so we initialize it to `0`. We then sum +1.
2. `log2` arrives: we examine if there is a group for `timestamp=12345`. Yes, there is an existing group. We load the `map` shared variable. We run `code =>` and see that `map['count_logs']` exists, so we skip the initialization. We sum +1 to the current value (now, `map['count_logs'] = 2`)

The `code => ` section often needs to access to some log value: we can do it with `event.get('fieldName')`. For example, suppose that each log has got a `duration` field indicating the event duration. We want to sum all the durations. Then, we can take its `duration` field and add it:
```JSON
code => "
	map['count_logs'] ||= 0
	map['count_logs'] += event.get('duration')
"
```
If we are operating with strings, we can use the `<<` to append the string. For example, suppose that each log has got a `message`. We want to append all the messages for the same `processId`. Then, we can take its `message` field and append it to the current one:
```JSON
code => "
	map['count_logs'] ||= ''
	map['count_logs'] << event.get('message')
"
```
**Example 1**
For example, the `proccessId` or the `timestamp`:
```JSON
log1: {"timestamp": "2024-05-10T11:10:10.211Z", "processId": 1, "message": "Java Exception"}
log2: {"timestamp": "2024-05-10T11:10:10.211Z", "processId": 1, "message": "    at java.lang.Object=..."}
log3: {"timestamp": "2024-05-10T11:10:10.211Z", "processId": 1, "message": "    at java.lang.Exception=..."}
```
As you can see, there is a Java process that triggers an exception that is:
```
Java Exception
	at java.lang.Object=...
	at java.lang.Exception=...
```
And, as you can see, each line in the log is wrapped apart from the others, in different JSON objects, whereas they should be treated in the **same message**, as they have been generated at the same timestamp exactly.
We want this result:
```JSON
log1: {"timestamp": "2024-05-10T11:10:10.211Z", "processId": 1, "message": "Java Exception\n\tat java.lang.Object=...\n\tat java.lang.Exception=..."}
```
Here is where aggregations come into play. We have a common field (`timestamp` or `processId`) and want to aggregate all logs that share the same field value.
The `aggregate` have three parts:
- The **beginning** of the log (in this case, `Java Exception` line)
- The **body** of the log (in this case, `at java.lang.Object=...` and `at java.lang.Exception=...`)
- The **end** of the log. In this case, there is no a line that suggests the end of the log, but we might find some cases where we have a log with end information such as:
```
Java Exception
	at java.lang.Object=...
	at java.lang.Exception=...
	Please refer to https://javahelp to find how to resolve this issue <------
```
We can create a code as in [[AWK]] to do something (e.g. initialize variables) in the first line of the log (i.e. in the beginning of the log, like the `BEGIN{}` pattern of AWK), to do something (e.g. the code itself) when reading the log body (like the AWK code itself) and to do something in the last line of the log (i.e. in the end of the log, like the `END{}` pattern of AWK).
To achieve the result in the example above, we have to use this pipeline code:
```JSON
filter{
	if [message] == "Java Exception" { # Here we detect the log start
		aggregate {
			task_id => "%{@timestamp}" # Here we define to aggregate by timestamp
			code => "         # Here we define the code to run for the first line
				map['myvariable'] = event.get('message')+'\n' # Here we initialize a new variable with the content of the message (=Java Exception) and append a \n
			"
		}
	}
	
	if [message] =~ "at(.*)" { # Here we detect the log body
		aggregate {
			task_id => "%{@timestamp}" # Here we define to aggregate by timestamp
			code => "         # Here we define the code to run for the first line
				map['myvariable'] << event.get('message')+'\n' # Here we define that we want to append this line to the current myvariable plus a \n
			"
		}
	}

	if [message] =~ "Please(.*)" { # Here we would detect the log end
		aggregate {
			task_id => "%{@timestamp}" # Here we define to aggregate by timestamp
			code => "         # Here we define the code to run for the first line
				event.set("message", map['myvariable'])" # Here we finally dump the content of myvariable into message field
			end_of_task => true # Specify that this is the end of this task aggregation
		}
	}
}
```
**Example 2**
For _Example 1_, we can do the same in a more elegant way: tell logstash to stop aggregating when timestamp changes:
```JSON
filter{
	aggregate{
		task_id => "%{@timestamp}"
		code => "
			map['message'] ||= ''
			map['message'] << event.get('message')+'\n'
		"
		push_previous_map_as_event => true
	}
}
```
###### prune{}
To drop all fields in a log and specify a whitelist of fields you want to keep.
For example:
```JSON
prune {
	whitelist_names => ["message"]
}
```
Would take this log event:
```JSON
{
	"event": {...},
	"data_stream": {...},
	"message": "7477: hello",
	"@timestamp": "...",
	...
}
```
And would result in:
```JSON
{"message": "7477: hello"}
```
##### output{}
_To see where you can dump data into, refer to [available output{} plugins](https://www.elastic.co/guide/en/logstash/current/output-plugins.html)_
Here we will define where to dump data. There can be more than one output. In that case, the processed data would be dumped sequentially in the order they are defined.
###### file{}
If you want to output data into a file, use `file{}` plugin. You can also set `codec => multiline` if you want to merge several logs into a single one by a regular expression.
##### codec => ... {}
Serves for changing data representation.

#### Access to JSON fields
Imagine that your incoming logs follow this format, which is, indeed, the output format of elastic agents:
```JSON
{
	"event":{
		"dataset":"dataset_name"
	},
	"data_stream":{
		"type":"logs",
		"namespace":"default",
		"dataset":"dataset_name"
	},
	"elastic_agent":{
		"id":"abcdefg-3456-1234...",
		"version":"8.12.2",
		"snapshot":false
	},
	"log":{
		"offset":813359,
		"file":{
			"path":"C:\\examples\\mylog.log"
		}
	},
	"host": {
        "architecture": "x86_64",
        "os": {
            "build": "22631.3593",
            "version": "10.0",
            "name": "Windows 11 Pro",
            "type": "windows",
            "kernel": "10.0.22621.3593 (WinBuild.160101.0800)",
            "platform": "windows",
            "family": "windows"
        },
        "name": "hostname",
        "hostname": "hostname",
        "id": "1841bb5b-269f-40ff-ad1c-10cf4bd2a7b9",
        "ip": [
            "1.2.3.4",
            "2.3.4.5"
        ],
        "mac": [
            "AA-15-BB-D7-CC-C8"
        ]
    },
    "@timestamp": "2024-05-20T18:00:50.742Z",
    "@version": "1",
    "agent": {
        "id": "43e416d9-457c-4f3d-aaa6-e365382d5886",
        "ephemeral_id": "df88f790-0de3-4cac-a279-8fb2ca3fb3fa",
        "version": "8.12.2",
        "name": "agentname",
        "type": "filebeat"
    },
    "message": "7477: hello", <---------------------------- The log payload is here
    "input": {
        "type": "log"
    },
    "tags": [
        "my_logs_tag"
    ],
    "ecs": {
        "version": "8.0.0"
    }
}
```
As you can see, the log payload is wrapped in a JSON with lots of fields. You can access to the message or other fields with this syntax:
- `[message]`
- `[tags]`
- `[ecs][version]`: to access `ecs.version`
For example, in your pipeline you can [[#Conditionals|check whether]] `message` is equal to something and then output data to different sites:
```JSON
output{
	if [agent][name] == "database_server" {
		file {
			path => "/tmp/database_server_logs.log"
		}
	}
	if [agent][name] == "web_server" {
		file {
			path => "/tmp/web_server_logs.log"
		}
	}
}
```
#### Conditionals
Syntax:
```JSON
if EXPRESSION {
	...
} else if EXPRESSION {
	...
} else {
	...
}
```
##### EXPRESSION
We can compare with [[#Equality comparison operators]], [[#Regular expression operators]], .[[#Inclusion/Exclusion operators]], ...
###### Equality comparison operators
| Operator |        Meaning        |
| :------: | :-------------------: |
|    ==    |       Equal to        |
|    !=    |      Distinct to      |
|    <     |       Less than       |
|    >     |     Greater than      |
|    <=    |  Less or equal than   |
|    >=    | Greater or equal than |
Example:
```JSON
if [host][name] == "database_server" {
	...
}
```
###### Regular expression operators
| Operator |      Meaning       |
| :------: | :----------------: |
|    =~    |    Matches with    |
|    !~    | Doesn't match with |

Example: to check if the hostname matches some regular expression:
```JSON
if [host][name] =~ "(.*)server" {
	...
}
```
###### Inclusion/Exclusion operators
| Operator | Meaning |
| :------: | :-----: |
|    in    |   In    |
|  not in  | Not in  |
Example: to check if the `tags` field contains some tag:
```JSON
if "my_tag" in [tags] {
	...
}
```
###### Boolean operators
Used to combine several expressions.

| Operator |
| :------: |
|   and    |
|    or    |
|   nand   |
|   xor    |
Example:
```JSON
if [host][name] ~= "(.*)server" and "server" in [tags] {
	...
}
```
### Worker
The execution thread that processes the data. If you set only one worker, all the events will pass through the same worker and then we will ensure that the events are processed in the order they arrive.
## Logstash configuration - logstash.yml
The global configuration of logstash. It allows configuring some parameters for all pipelines, such as the [[#Worker|number of workers]] for all pipelines.
### Configurations files location
#### Linux local installations
- `/etc/logstash/logstash.yml`
## Pipelines configuration
Pipelines can be configured by [[#Pipelines configurations - pipelines.yml|pipelines.yml]].
The processing itself can be programmed through [[#Pipeline programming - *.conf|*.conf files]].
### Pipelines configurations - pipelines.yml
It allows configuring some parameters for each pipeline, such as the [[#Worker|number of workers]] for that pipeline.
#### Linux local installations
- `/etc/logstash/pipelines.yml`
### Pipeline programming - \*.conf
#### Linux local installations
- `/etc/logstash/conf.d/*.conf`
# Hands on
## Configure logstash
Go to [[#Logstash configuration#Configurations files location|logstash.yml]]. The available configurations are:
### pipeline.workers
You can configure how many [[#Worker|workers]] to use and apply that configuration to all pipelines in [[#Logstash configuration - logstash.yml|logstash.yml]] with this setting:
```YAML
pipeline.workers: 1
```
However, if you want to set this configuration separately for each [[#Pipelines|pipeline]], you can do it in [[#Pipelines configurations - pipelines.yml|pipelines.yml]]:
```YAML
pipeline.id: ...
path.config: "/etc/logstash/conf.d/<pipeline.conf>"
pipeline.workers: 1          <---
```
## Create a pipeline
_Refer to [Creating a Logstash pipeline](https://www.elastic.co/guide/en/logstash/current/configuration.html)_
Go to [[#Pipeline programming - *.conf|.conf files]] and create your `.conf` file.
### Example template
```JSON
input
{
	...
}
#----------------------------------------------------------------------------------
filter
{
	...
}
#----------------------------------------------------------------------------------
output
{
	...
}
```
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