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
Similarly to [[SNMP#Core principles|SNMP Agent]], the Elastic Agent is a process that runs on the target machine [[Internet#Terminal systems|host]] that we want to monitor. Then, the [[#Fleet Server]] will make requests to retrieve some data of this agent.
However, this Elastic Agent is also capable of monitoring external host data (from other machines), as it is depicted here:
![[ElasticAgentCorePrinciples.png]]
_Elastic Agent can monitor the host where it’s deployed, and it can collect and forward data from remote services and hardware where direct deployment is not possible_
However, not every service can be exposed by the Elastic Agent: that service must be an [[#Integration]].
##### Integration
To connect your data to Elastic Stack. Basically it is the bridge that connects a service with the Agent.
![[ElasticAgentCorePrinciplesIntegrations.png]]
Use **Elastic Package Registry** to download Integrations packages.
#### Policy
To specify which integrations you want to run and on which hosts. This means that you might have more than one host and each host may need different services to monitor:
![[ElasticAgentCorePrinciplesPolicies.png]]
You can even apply 1 policy to N Agents.
##### Fleet mode
You configure the policies through [[#Fleet]] User Interface (easy way).
##### Standalone mode
You specify the policies in a [[YAML]] file (more advanced).

### Fleet Server
_It's not the same as Fleet!_
Similarly to [[SNMP#Core principles|SNMP Manager]], the Fleet Server is a process that runs on the machine [[Internet#Terminal systems|host]] that monitors the [[#Elastic Agent]] by making requests to that running agent.
You need to connect to that Fleet Server in order to see application data. One way is through [[#Fleet]].
#### Fleet
It is available in [[Kibana]]. It involves an UI (User Interface) to explore agents.
### Hands on
#### Create a service to be monitored
#### Deploy the Elastic Agent on Windows as Docker Container
Refer to [Run Elastic Agent in a container | Fleet and Elastic Agent Guide [8.12] | Elastic](https://www.elastic.co/guide/en/fleet/current/elastic-agent-container.html).
You can run the Elastic Agent as a [[Docker|Docker container]].
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
#### Deploy the Fleet Server on a Virtual Machine
##### Deploy Kibana as a container
You will need

# CLOUD: PROVISION, MANAGE AND MONITOR THE ELASTIC STACK

# References
- [Elastic Products - Entry Point of Documentation](https://www.elastic.co/guide/index.html)