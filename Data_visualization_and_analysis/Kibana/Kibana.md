---
tags:
  - Data_visualization_and_analysis
---
# Core principles
_Most steps detailed here are very similar to the steps defined in [[ElasticSearch]]_
## Configuration Files
To Do
### Main Configuration Files
Kibana can be mainly configured through three files:
- ```kibana.yml```
### Configuration Files Path
#### For locally running Kibana
You will find the ElasticSearch files in the following path:
```
/etc/kibana
```
But if you are running Kibana in some directory because you are testing it (e.g. you are running Kibana in Downloads folder), by default the configuration files path would be:
```
<path_to_extracted_kibana>/config
```
#### For Docker-deployed Kibana
You will find the Kibana files in the following path:
```
/usr/share/kibana/config
```
### Important configurations
#### 0. Target ElasticSearch Host
#### 1. Username and Password for ElasticSearch
[[ElasticSearch#1. Kibana user|By default]], Kibana has got its own user inside ElasticSearch, called _kibana_system_. The first time you run [[ElasticSearch]], ElasticSearch sets an automatic password for Kibana and shows it in terminal. However, you might have lost it or forgotten it, so you can [[ElasticSearch#elasticsearch-reset-password]]. Whatever the case, once you have the password for Kibana, set it in `kibana.yml`:
```kibana.yml
elasticsearch.username: "kibana_system"
elasticsearch.password: "..."
```
# Hands on
The best way to learn Kibana is firstly [[#Kibana Locally|work locally]] (without docker containers).
## Kibana Locally
- You can download Kibana [[BinaryFile|Binary Files]] from [here](https://www.elastic.co/es/downloads/kibana). Extract the ```.tar.gz``` file, look for ```/bin``` directory.
- However, it's recommended to install Kibana with Debian Package (refer to [Install Kibana with Debian package | Kibana Guide [8.13] | Elastic](https://www.elastic.co/guide/en/kibana/current/deb.html)), as the configuration files will be automatically placed where they should be. Remember the [[Linux#APT|steps]] for installing Debian packages.
