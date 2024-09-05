---
tags:
  - Others
  - Elastic
  - Ingest_add_your_data
---
# Core principles
# Release notes
_Refer to [Release notes | Beats Platform Reference [8.13] | Elastic](https://www.elastic.co/guide/en/beats/libbeat/current/release-notes.html)_
In the Release notes there are all the available versions for Beats products.
For each version in Release notes, you can see the compatibility of Beats with different [[Operating System|Operating Systems]] in [[Elastic Products#Support Matrix]].
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
For Windows, ensure that the path is written with `\\` (e.g. `"C:\\"`, `C:\\Users\\me` etc).
#### ElasticSearch
```YAML
output.elasticsearch:
	hosts: ["ES_IP:ES_PORT"]
```
##### HTTPS
```YAML
output.elasticsearch:
  hosts: ["https://ES_IP:ES_PORT"]
  protocol: "https"
  ssl.certificate_authorities:
    - path/to/ca.crt
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
### Download MetricBeat package
_Refer to `https://www.elastic.co/es/downloads/past-releases/metricbeat-MAJOR-MINOR-PATCH`_
For example, to install MetricBeat 6.5.4: `https://www.elastic.co/es/downloads/past-releases/metricbeat-6-5-4`.
### Compatibility check
If you want to check whether the installed MetricBeat package is compatible with your OS, you can perform a compatibility check.
#### On Windows
Go to the downloaded `.zip` package and unzip it. Look for `metricbeat.exe` and move to the folder where it is located. Then, run:
```cmd
metricbeat test config
```
### Install
#### On Windows
Go to the downloaded `.zip` package and unzip it. Look for `metricbeat.exe` and [[Windows#Useful commands#CMD|run it]].
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
# LibBeat
A library written in [[Go]] that contains the [[Internet#API|API]] that all **Beats** use for shipping data to ElasticSearch. With LibBeat, you can use this library to create your own Beat.
The developer community has released custom Beats (such as [[AIX]]Beat, called **PerfStatBeat**, which is a version of [[#MetricBeat]] but adapted to [[AIX]] [[Operating System]]). you can find them here:
[Community Beats | Beats Platform Reference [master] | Elastic](https://www.elastic.co/guide/en/beats/libbeat/master/community-beats.html)
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

