---
tags:
  - Others
  - Elastic
  - Ingest_add_your_data
---
# Core principles

# AuditBeat
Used for 

# FileBeat
Used for collecting, grouping and dumping **logs** from some input path inside the machine it is installed to different outputs.
## filebeat.yml
FileBeat can be configured by `filebeat.yml`, which is under the following path:
- For [[Linux]] OS, `/etc/filebeat/filebeat.yml`
- For [[AIX]] OS, `/opt/freeware/etc/filebeat/filebeat.yml
With `filebeat.yml` you can configure the [[#filebeat.yml#Output]] (e.g. [[ElasticSearch]], [[Logstash#Run Logstash Server for Elastic Beats|Logstash Server]], a local file on the machine itself...), as well as the [[#filebeat.yml#Input]].
### Input
The path where from to read logs inside the instrumented machine.
```YAML
filebeat.inputs:
	- type: log
	enabled: true
	paths:
		- /path/to/logs
		- /path/to/logs
		- /path/to/logs
		- ...
```
### Output
```YAML
output.elasticsearch:
	hosts: ["ES_IP:ES_PORT"]
```
## Binary path
- For [[Linux]] OS, `/usr/share/filebeat/bin/filebeat`
- For [[AIX]] OS, `/opt/freeware/usr/share/filebeat/bin/filebeat`
## Logs path
- For [[Linux]] OS, `/var/log/filebeat/filebeat.X` or in `/var/log/syslog`
- For [[AIX]] OS, `/opt/freeware/var/log/filebeat/filebeat.X`
## Hands on
### Install
#### On Linux
It is recommended to install FileBeat as [[Linux#APT#Debian package .deb|Debian package]] following these steps: [[Linux#Install a Debian package]] to automatically place the FileBeat files where they should be.
#### On AIX
To install FileBeat on an [[AIX]] [[Operating System]], you should get the [[Linux#RPM file]]. Once you have it, [[Linux#Install a rpm package|install it]].
Supposing you want to get the FileBeat `.rpm` package, as this file cannot be found directly from the official website, the recommended way to proceed is:
1. Get access to the [[AIX]] system through [[SSH]]
2. Perform a [[Linux#DNF#Redownload installed packages but not installing]] setting the `--downloaddir` to some directory you can control
3. Go to the `.rpm` location and export it running a [[SSH#scp (Secure Copy)]] command from the machine that will receive the package
Once downloaded, you can [[AIX Subsystem|convert it into a subsystem]].
# FunctionBeat
# HeartBeat
# MetricBeat
Used for monitoring **services** (also called **modules**) on the machine where it is installed. Specifically, it gets **metrics** (i.e. CPU usage, memory usage and more).
## metricbeat.yml
MetricBeat can be configured by `metricbeat.yml`, which is under the following path:
- For [[Linux]] OS, `/etc/metricbeat/metricbeat.yml`
- For [[AIX]] OS, `/opt/freeware/etc/metricbeat/metricbeat.yml
With `metricbeat.yml` you can configure the [[#metricbeat.yml#Output]] (e.g. [[ElasticSearch]], [[Logstash#Run Logstash Server for Elastic Beats|Logstash Server]], a local file on the machine itself...).
### Output
#### File
```YAML
output.file:
	path: '/path/to/output/file'
	filename: '<output_filename>'
```
#### ElasticSearch
```YAML
output.elasticsearch:
	hosts: ["ES_IP:ES_PORT"]
```
## Binary path
- For [[Linux]] OS, `/usr/share/metricbeat/bin/metricbeat`
- For [[AIX]] OS, `/opt/freeware/usr/share/metricbeat/bin/metricbeat`
## Logs path
- For [[Linux]] OS, `/var/log/metricbeat/metricbeat.X` or in `/var/log/syslog`
- For [[AIX]] OS, `/opt/freeware/var/log/metricbeat/metricbeat.X`
## Metricbeat modules path
Modules to be monitored are configured as [[YAML]] files under the following path:
- For [[Linux]] OS, `/etc/metricbeat/modules.d/`
- For [[AIX]], `/opt/freeware/etc/metricbeat/modules.d/`
## Hands on
### Install
#### On Linux
It is recommended to install MetricBeat as [[Linux#APT#Debian package .deb|Debian package]] following these steps: [[Linux#Install a Debian package]] to automatically place the MetricBeat files where they should be.
#### On AIX
To install MetricBeat on an [[AIX]] [[Operating System]], you should get the [[Linux#RPM file]]. Once you have it, [[Linux#Install a rpm package|install it]].
Supposing you want to get the MetricBeat `.rpm` package, as this file cannot be found directly from the official website, the recommended way to proceed is:
1. Get access to the [[AIX]] system through [[SSH]]
2. Perform a [[Linux#DNF#Redownload installed packages but not installing]] setting the `--downloaddir` to some directory you can control
3. Go to the `.rpm` location and export it running a [[SSH#scp (Secure Copy)]] command from the machine that will receive the package
Once downloaded, you can [[AIX Subsystem|convert it into a subsystem]].
# PacketBeat
# WinlogBeat
## WinLog fields
Besides [[#Log fields|generic log fields]], Windows logs contain the following fields:
### winlog.channel
It can be one of these options:
- Application
- Security
- System
### winlog.computer_name
The name of the computer that generated the record. It can be in URL format (e.g. `my_machine.informatics.enterprise.com.es`).
### winlog.event_data
The specific data used by the process that triggered the event. E.g.:
- A URL that contains a `.crt` file
- A code error message
- Process ID
- Process state
- Username (e.g. to know if the process was launched by some administrator)
### winlog.keywords
Similar to generic [[#tags]], the keywords used to classify events. E.g.: `Classic`, `Audit Success` (when some user logs in successfully), `Audit Failure` (when there are errors in log in process)... With the difference that `windows.keywords` is exclusive for Windows.
### winlog.provider_name
The same as [[#event.provider]] but for Windows.
# Log fields
## log.level
The log level (`info`, `warning`, `error`...).
## message
The log message, optimized for viewing in a log viewer.
### event.original
Raw text message of entire event (the full log message).
## event.provider
The source of the event. It can be:
- The name of the Software that generated the event
- Subsystem of the OS (e.g. kernel)
## event.created
When the event was read/processed by the agent.
### @timestamp
The time extracted from the original event.
## event.kind
High-level information about what type of information the event contains. For instance: `event`.
### event.category
For instance: `process`, `authentication`.
#### event.type
For instance: `info`, `authentication_success`.
##### event.action
The action captured by the event. For instance, `group-add`, `process-started`, `file-created`.
###### event.outcome
Simply denotes whether the event represents a success or a failure from the perspective of the entity that produced the event.
This field is generally not populated for metric events, events with [[#event.type|event.type=info]], or any events for which an outcome does not make logical sense.
For example, if there are errors in user log in processes, the `event.outcome` will be `failure` and will be `success` otherwise.
## tags
Similarly to [[#winlog.keywords]], `tags` are the list of keywords used to tag each event.

