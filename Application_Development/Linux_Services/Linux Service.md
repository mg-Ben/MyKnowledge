---
tags:
  - Application_Development
---
# Core principles
A background process that is started and stopped on certain circumstances. Often called **daemon**.
There are software suite available for [[Linux]] [[Operating System|Operating Systems]] such as [[#systemd]].
![[systemd-services-example.png]]
## systemd
A software suite for creating [[Linux]] [[Operating System|Operating Systems]] services.
### Unit file
The file where you define your service and its configuration. It must have the ```.service``` extension.
Located under ```/etc/systemd/system``` path for ```superusers```, and under ```~/.config/systemd/user/``` for normal users. You will need to create it if not created:
```shell
mkdir -p ~/.config/systemd/user
```
See [[Linux#/etc]].
It consists of several sections.
#### Unit section
Here is where you define a description and whether your service must run after or before another service, or both.
- **Description**
- **After**
- **Before**
#### Service section
Details about the execution. For instance, here is where you define the command that must be run on service start.
- **ExecStart**: the command that is executed when the service starts. If it runs a [[Bash Scripting|Bash Script]], you can either specify ```/usr/bin/bash/ <path_to_sh_file>``` or directly ```<path_to_sh_file>```
- **Restart**: to specify when your service needs to be restarted depending on certain circumstances (for example, ```no```, ```on-success```, ```on-failure```, ```on-abnormal```, ```on-watchdog```, ```on-abort``` or ```always```)
- **WorkingDirectory**: In case your Bash Script has some file dependencies, here you can specify the path where the ```.sh``` file is located to also reference those files
#### Install section
Handles the installation of your service. This section is used when running ```systemctl enable``` and ```systemctl disable```.
- **Wanted-By**: similar to [[#Unit section|After and Before]], here you can set that the service would run after system initialization (i.e. when the user is asked to log in into the OS), for example (```default.target```)
- **RequiredBy**: Hard dependencies. The service would fail if the service it depends on fails
#### Example template
```
[Unit]
Description=Apache web server
After=network.target
Before=nextcloud-web.service

[Service]
ExecStart=/usr/local/apache2/bin/httpd -D FOREGROUND -k start ExecReload=/usr/local/apache2/bin/httpd -k graceful Type=notify Restart=always 

[Install]
WantedBy=default.target
RequiredBy=network.target
```
# Hands on
## systemd software suite
### List current services
```shell
systemctl list-units
```
Interesting flags:
- `--state=<state> (e.g. active, loaded, failed)`: filter output by unit state
### Start service
```shell
systemctl start <service_name.service>
```
### Get service status
_To get [[#Unit file]] location, service status, service Porcess ID, some logs..._
```shell
systemctl status <service_name.service>
```
### Stop service
```shell
systemctl stop <service_name.service>
```
### Restart service
```shell
systemctl restart <service_name.service>
```
### Daemon-reload
```shell
sudo systemctl daemon-reload
```
### Enable a service
Whenever you enable a service, you make it run automatically on startup.
```shell
sudo systemctl enable <file.service>
```
### Disable a service
Whenever you disable a service, you prevent it from running automatically on startup.
```shell
sudo systemctl disable <file.service>
```
### Reload a service
Reloading a service implies updating the configuration files without interrupting the service. It is useful when we have made changes to some configuration file (say [[Prometheus#prometheus.yml|prometheus.yml]], for example) and we don't want to restart the entire service.
```shell
sudo systemctl reload <service_name.service>
```
### Kill service
Get the Main Process ID from [[#Get service status|service status]] and run:
```shell
sudo kill <Process_ID>
```
### See service [[#Unit file]]
```shell
sudo systemctl cat <service_name.service>
```
### Show service details
```shell
sudo systemctl show <service_name.service>
```
### See service logs
There are several ways:
- Go to [[Linux#/var|/var]]/log/<service_name> directory
- Run `journalctl -u <service_name> -n <last n lines>` (`-u` stands for [[#Unit file|unit]], that is to say, the `<service_name>`)
_To see logs in real-time, you can use `tail` command with the `-f` (follow journal) flag (refer to [[UNIX#tail (see the last lines of some file)]]_
Some services have their own `log` files location, such as [[ElasticSearch]]. In this case, you can also use [[UNIX#tail (see the last lines of some file)]] command.
### Daemonize Bash command
You can daemonize a [[GNU#Bash#Useful commands|GNU or UNIX-standard command]] (i.e. turn the command process into a background process) with systemd. To do so, you will need to follow these steps:
1. Define the [[#Unit file]] for the Script specifying the command to daemonize.
2. Run ```systemctl [--user] daemon-reload```
3. Enable the service with ```systemctl [--user] enable <your_unit_file>.service```
4. Run ```systemctl [--user] restart <your_unit_file>.service```
The ```--user``` option will be necessary in case you defined the [[#Unit file]] for normal user.

### Common issues
#### Failed at step EXEC spawning "binary": Permission denied
This typically occurs when the `<binary>` file is located in an unsuitable directory and is probably related to [[SELinux]]. It is recommended to place it under `/sbin` or `/usr/bin` if possible.
In case you cannot place it there, then:
1. [[SELinux#Get File SELinux Context]] and ensure that the binary file is of type `bin_t`
2. If it is not `bin_t`, change it with [[SELinux#Set File SELinux Context]]