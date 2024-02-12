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
systemctl status
```
### Daemonize Bash command
You can daemonize a [[GNU#Bash#Useful commands|GNU or UNIX-standard command]] (i.e. turn the script process into a background process) with systemd. To do so, you will need to follow these steps:
1. Define the [[#Unit file]] for the Script specifying the command to daemonize.
2. Run ````systemctl [--user] daemon-reload```
3. Enable the service with ```systemctl [--user] enable <your_unit_file>.service```
4. Run ```systemctl [--user] restart <your_unit_file>.service```
The ```--user``` option will be necessary in case you defined the [[#Unit file]] for normal user.