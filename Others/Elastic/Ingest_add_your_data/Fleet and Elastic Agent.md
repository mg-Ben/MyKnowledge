---
tags:
  - Others
  - Elastic
  - Ingest_add_your_data
---
# Core principles
It servers for monitoring mainly application **logs** and **metrics**.
It is an alternative to [Elastic Beats](https://www.elastic.co/guide/en/beats/libbeat/current/beats-reference.html), that have the same purpose. However, whereas [[#Beats]] are lightweight, you will have to install separate agents for each type of data you want to monitor (one for Metrics, another one for logs, another one for files...).
See [Elastic Agent or Beats?](https://www.elastic.co/guide/en/fleet/current/beats-agent-comparison.html) to know which option is better.
## Elastic Agent
Similarly to [[SNMP#Core principles|SNMP Agent]], the Elastic Agent is a process that runs on the target machine [[Internet#Terminal systems|host]] that we want to monitor.
Unlike [[SNMP]], the agent communicates directly to the database (ElasticSearch) as depicted here:
![[ElasticAgentCorePrinciples.png]]
_Elastic Agent can monitor the host where it’s deployed, and it can collect and forward data from remote services and hardware where direct deployment is not possible_
However, not every service can be exposed by the Elastic Agent: that service must be an [[#Integration]].
### Integration
_Integration = What we want to monitor (e.g. The "System" integration is for monitoring system data such as CPU load)_
For example, here you can see some integrations. They are like "plugins" you can implement to monitor the target services:

![[integration-examples.png]]

There is a **Fleet Server** integration to manage Fleet Servers.
Therefore, integrations provide the bridge to manage different services:
![[ElasticAgentCorePrinciplesIntegrations.png]]
Use **Elastic Package Registry** to download Integrations packages.
### Policy
Consists of N [[#Integration|Integrations]].
![[1_policy_N_integrations.png]]

Policy serves to specify which [[#Integration|integrations]] you want to run and on which hosts. This means that you might have more than one host and each host may need different services to monitor:
![[ElasticAgentCorePrinciplesPolicies.png]]
You can even apply the same 1 policy to N Agents.
#### Fleet mode policy
You configure the policies through [[#Fleet]] User Interface (easy way).
#### Standalone mode policy
You specify the policies in a [[YAML]] file (more advanced).
## Fleet Server
It is an [[#Elastic Agent]] but configured as a **Fleet Server** (i.e. enrolled in a [[#Policy]] called **Fleet Server Policy**). The **Fleet Server Policy** is a policy that contains **Fleet Server Integration**.

![[FleetServer_Agents.png]]

### Fleet
It is available in [[Kibana]]. It involves an UI (User Interface) to explore agents.
To access Fleet:
- Deploy [[Kibana]]
- Go to **Management > Fleet**
# Hands on
You have to follow these steps, in this order:
1. [[#Deploy the Fleet Server]]
2. [[#Deploy the Elastic Agent|Deploy the Elastic Agent and connect it to the currently running Fleet Server]]
## Deploy the Fleet Server
### On-premises (self-managed Fleet Server)
_Refer to [Deploy on-premises and self-managed](https://www.elastic.co/guide/en/fleet/8.12/add-fleet-server-on-prem.html)_
_You will need to do it through [[#Fleet]]_
For example, you will use this approach to manually install [[#Fleet Server]] inside some host (e.g. inside a [[Virtualization#Guest Operating System]], or inside the machine itself).
1. [[#Fleet|Access to Fleet]]
2. **Go to Agent policies tab**: Create an [[#Policy|Agent Policy]] which represents the **Fleet Server Policy**. This is, a policy that contains Fleet Server [[#Integration]].
3. **Go to Settings tab**: Configure:
	1. [[#Fleet server host]]: the `IP:port` that others will use to connect to Fleet Server from outside
	2. [[#Agent output|Output for this Fleet Server agent]]. the `IP:port` that agents will use to send data. It can be, for instance, ElasticSearch or Logstash `IP:port`. If it is Logstash, see [[Logstash#Run Logstash Server for Elastic Agents]]
	3. [[#Install Fleet Server]]
#### Install Fleet Server
_Go to https://www.elastic.co/downloads/elastic-agent and download the software package.
Installing a Fleet Server is the same as installing any Elastic Agent, but in the installation command It is needed to specify different flags from those for generic Elastic Agents_
##### Add a Fleet Server
You can go to Kibana > Management > Fleet > Agents tab and press *Add Fleet Server* option. You will be provided the command to install the Fleet Server automatically, as well as other options (go to Advanced) such as whether you want the Elastic Agents to trust Fleet Server (production mode) or not (`--insecure` flag):

![[ssl-tls-in-fleetserver.png]]
- In Quick Start, you don't need to generate SSL/TLS certificate for Fleet Server. Therefore, [[#Interesting flags for Elastic Agent Installation|use --insecure flag]] when installing the remote Elastic Agents to connect to your already existing Fleet Server
- In Production mode, you have to generate SSL/TLS certificate for Fleet Server and make the remote Elastic Agents trust the [[Cybersecurity#Certificate Authorities (CA)|CA]] that generated that certificate
##### Windows
Once you have downloaded the [Elastic Agent software-package](https://www.elastic.co/downloads/elastic-agent) and unzipped it (if needed), go inside the downloaded folder (typically `C:\Users\<username>\elastic-agent...`) and look for `elastic-agent` executable file. Then, run this command in [[Windows#PowerShell]] as Administrator:
```powershell
.\elastic-agent.exe install
```
See [[#Interesting flags for Fleet Server Installation]].
##### Linux
Once you have downloaded the [Elastic Agent software-package](https://www.elastic.co/downloads/elastic-agent) and unzipped it (if needed), go inside the downloaded folder and look for `elastic-agent` executable file. Then, run:
```shell
sudo ./elastic-agent install
```
See [[#Interesting flags for Fleet Server Installation]].
##### Interesting flags for Fleet Server Installation
- `--fleet-server-es=<ES_URL>`: the [[#Agent output|URL of ElasticSearch]]
- `--fleet-server-service-token=<Policy-token>`: each [[#Policy]] has got its own enrollment token so as to attach a policy to an agent. To access them, go to Kibana > Management > Fleet > Enrollment tokens
- `--fleet-server-policy=<Policy-name>`: the [[#Policy]] name. You must write it replacing blank spaces with `-` and capital letters with lowercase letters
- `--fleet-server-es-ca-trusted-fingerprint=<ElasticSearch-CA-fingerprint>`: the ElasticSearch Certificate Authority's fingerprint (see [[SSL-TLS#Get Fingerprint from .crt]] on how to get fingerprint). You must write it removing `:` (e.g.: `EF34F124D98...`)
- `--fleet-server-port=<FleetServerPort>`: the port that will be used to run Fleet Server
The installation command for Fleet Server would look like this:
```shell
./elastic-agent install \
	--fleet-server-es=https://... \
	--fleet-server-service-token=... \
	--fleet-server-policy=... \
	--fleet-server-es-ca-trusted-fingerprint=... \
	--fleet-server-port=...
```
## Deploy the Elastic Agent
_Go to https://www.elastic.co/downloads/elastic-agent and download the software package._
### Install Elastic Agent
##### Windows
Once you have downloaded the [Elastic Agent software-package](https://www.elastic.co/downloads/elastic-agent) and unzipped it (if needed), go inside the downloaded folder (typically `C:\Users\<username>\elastic-agent...`) and look for `elastic-agent` executable file. Then, run this command in [[Windows#PowerShell]] as Administrator:
```powershell
.\elastic-agent.exe install
```
See [[#Interesting flags for Elastic Agent Installation]].
##### Linux
Once you have downloaded the [Elastic Agent software-package](https://www.elastic.co/downloads/elastic-agent) and unzipped it (if needed), go inside the downloaded folder and look for `elastic-agent` executable file. Then, run:
```shell
sudo ./elastic-agent install
```
See [[#Interesting flags for Elastic Agent Installation]].
##### Interesting flags for Elastic Agent Installation
- `--url=https://<FleetServerIP>:<FleetServerPORT>`: the Fleet Server URL
- `--enrollment-token=<Policy-token>`: the [[#Policy]] enrollment token to which attach this Elastic Agent. Each [[#Policy]] has got its own enrollment token so as to attach a policy to an agent. To access them, go to Kibana > Management > Fleet > Enrollment tokens
- `--insecure`: to skip SSL/TLS verification. With this setting, we prevent Elastic Agent from ensuring that the Fleet Server is reliable
### Uninstall Elastic Agent
#### Windows
Run `C:\"Program Files"\Elastic\Agent\elastic-agent.exe uninstall` in [[Windows#PowerShell]]. You must be on the **PowerShell** as Administrator (otherwise the command won't be recognized by cmdlet) and outside the `C:\"Program Files"\Elastic` directory (i.e. in `C:\"Program Files"`).
- If `elastic-agent.exe` file is missing, you will have to [[#Install Elastic Agent#Windows]] again and let the process create the `.exe` file
#### Linux
```shell
sudo ./usr/bin/elastic-agent uninstall
```
If the command output is:
`Error: can only be uninstalled by executing the installed Elastic Agent at: /usr/bin/elastic-agent`
Then it means that the Elastic-Agent is partially installed and broken. To solve this issue:
1. Stop the current `elastic-agent.service` with `sudo systemctl stop elastic-agent.service`
2. Disable the current `elastic-agent.service` with `sudo systemctl disable elastic-agent.service`
3. Remove the `elastic-agent.service` file with `sudo rm /etc/systemd/system/elastic-agent.service`
4. Remove the `/opt/Elastic/Agent` directory with `sudo rm -r /opt/Elastic/Agent`
### See Elastic Agent logs
#### Windows
Go to `C:\\"Program Files"\\Elastic\\Agent\\data\\elastic-agent-*\\logs` and look for `.ndjson` files.
#### Linux
Go to `/opt/Elastic/Agent` and look for `.ndjson` files.
### Docker
Refer to [Run Elastic Agent in a container | Fleet and Elastic Agent Guide [8.12] | Elastic](https://www.elastic.co/guide/en/fleet/current/elastic-agent-container.html).
You can run the Elastic Agent as a [[Docker|Docker container]]. In the configuration file, you have to set that the agent will enroll to the [[#Fleet Server]] (i.e. will subscribe to it) so that the Fleet Server communicate with it, specifying the Fleet Server URL.
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
## Stop the Fleet Server
##### Linux
```shell
systemctl stop elastic-agent.service
```
## Common settings
#### Agent output
Here you will configure the `IP:port` where the [[#Elastic Agent|Elastic Agents]] will dump data (specifically, you configure the HTTP URL, and HTTP**S** is mandatory). Remember that [[#Fleet Server]] is also an Elastic Agent, so this configuration applies to both: Fleet Server Agent and Agents.
##### What is the IP:port?
As the **Fleet > Settings** tab is applied to every agent that will be installed, the output `IP:port` would be the same for Fleet Server Agent and remote Elastic Agents: however:
- If **Fleet Server Agent** is installed in the same machine as ElasticSearch for example, **Fleet Server Agent** connects to ElasticSearch through `localhost:<ES_PORT>`
- But if other **Agents** are installed on other machines, they would connect to ElasticSearch through `<ES_IP>:<ES_PORT>` (not localhost), so the configuration can't be the same for one and the other!_

![[fleet-server-elastic-agents.png]]
In these cases, you can configure the output as `<ES_IP>:<ES_PORT>` (in the example diagram, `1.2.3.4:9200`) even for Fleet Server Agent, because inside the Host you can connect to the interface IP itself:

![[fleet-server-elastic-agents-corrected.png]]
In the example above, `1.2.3.4:9200` is where we can find ElasticSearch process running, but the truth is that in that `IP:port` there might be a Logstash server or whatever. You can even configure >1 output.
##### How to configure SSL/TLS?
It will depend on the output.
###### ElasticSearch Output
As HTTP**S** is mandatory when installing Fleet Server, ElasticSearch must be secured with TLS/SSL. Therefore, firstly you will have to [[ElasticSearch#4. Security|configure SSL/TLS inside ElasticSearch]].
Once you have configured it, you must get the [[ElasticSearch#Generate Certification Authority|CA certificate file (.crt)]] and then the [[SSL-TLS#OpenSSL#Get Fingerprint from .crt|fingerprint]]. Copy it without the character `:` and paste in the Kibana > Management > Fleet > Settings > Outputs > ElasticSearch > *Elasticsearch CA trusted fingerprint (optional)*.

_In this way, Elastic Agents will trust the `https://ES_IP:ES_PORT` URL, as you have set inside Kibana > Fleet the CA that your Elastic Agents must trust in._
###### Logstash Output
Firstly, you must ensure that your Logstash instance is running as a server.
#### Fleet server host
Here you will configure the `IP:port` where [[#Fleet Server]] is publicly waiting for connections.
Remember that one host has got [[IP#IP address|several network interfaces]]. Consequently, here you choose which network interface IP your Fleet Server will be hearing on.
_Example: if you set this configuration to `1.2.3.4:8220`, the Fleet Server Agent will be hearing at that `IP:port` and then will be accessible from outside using that `IP:port` tuple:_

![[fleet-server-elastic-agents-corrected-fleetserverhostIP.png]]

Remember that the `0.0.0.0` [[IP#IP address]] is equivalent to "All network interfaces", so if you set the public IP address to `0.0.0.0` the Fleet Server will be hearing on all network interfaces inside the host it is running.
## Output indexes
Once the Elastic Agents are deployed and outputting data, you can [[ElasticSearch#Explore data|explore the Elastic Agent Data in ElasticSearch]]. The Elastic Agent output data is in data stream format.