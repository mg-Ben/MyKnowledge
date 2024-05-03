---
tags:
  - Computer_Systems
  - Operating_Systems
---
# Core principles
It is a [[UNIX|UNIX-like]] [[Operating System]].
# Users
The [[Operating System]] is shared between several users. Each user has got its own password, and belongs to one or more group. However, one of those groups is considered the primary group.
## User types
### System users
Non-human users automatically created by the Operating System and able to run [[Linux Service|services]] or applications. They cannot log in to the system.
### Regular users
Human users who can log in to the system. They belong to a group.
### Super/Root users
System administrators to perform high-level tasks that might be dangerous for system. Also known as **root** users. It hasn't got a password.
## User groups
### Sudoers
Basically the users which can switch to [[#Super/Root users]], i.e. system administrators.
## Hands on
### List users
```shell
cat /etc/passwd
```
See [[#/etc]].
Example output:
![[example-output-listusers.png]]
### Switch user
```shell
su - <username>
```
### Run some command as another user
```shell
sudo -u <another_user> <command>
```
This is very useful for users who don't have their own [[Linux#/home]] directory, such as system users (`elasticsearch` or `kibana` users).
#### Switch to root user
You, as a user, must be registered in [[#Sudoers]] group to switch to root user. Then, run this command and enter your user password:
```shell
sudo -i
```
The root user mode is now enabled:
![[root-user-mode.png]]
#### Run commands as root user
In this case, you can run commands which require [[#Super/Root users]] permissions but without the need to enter root user mode; i.e. the command would run in root user mode but after executing it, you would return to regular user mode.
```shell
sudo <command>
```
### Get user group
```shell
groups <username>
```
#### Get sudoers
```shell
cat /etc/sudoers
```
See [[#/etc]].
# File system
A method for storing and organizing data on computer. It follows a **tree structure** that starts from the **root**  ```/``` directory. Each file has got metadata such as a **type** and **permissions**, as well as a owner and group owner. Besides, it has a size in bytes.
## File owners
### File owner
The [[#Users|User]] who created the file.
### Group owner
When a file is created inside a directory, the group owner of the file is the same as the group owner of that directory where it is created. In other words, the group owner would be the primary group of the [[#File owner]].
## File type and permissions
File type and permissions has a format like:
```
drw-r--r--x
```
For example:
![[file-permissions-la.png]]
To understand them, we will split it into several parts:
```
-                  rw-r--r--               .
^                  ^                       ^
File type          File permissions        Extended permissions
```
- The first character (in the example above, ```d```) is file type
- The following 9 characters (in the example above, ```rw-r--r--```) are file permissions:
	- The first three characters (```rw-```) are file permissions for the [[#File owner]]
	- The second three characters (```r--```) are file permissions for the [[#Group owner]]
	- The third and last three characters (```r--```) are file permissions for **others**
### File types
- ```-``` means regular file
- ```d``` means directory
- ```b``` means block file
- ```c``` means character device file
- ```p``` means name pipe or pipe file
- ```l``` means symbolic link file
- ```s``` means socket file
### Permissions
- ```r``` means read
- ```w``` means write
- ```x``` means execute
The ```root root``` part means:
- The first word (```root```) is the [[#File owner]]
- The second word (```root```) is the [[#Group owner]]
## Typical structure
![[linux-filesystem.png]]
### /bin
_Binaries_
Contains [[BinaryFile|Binary files]] such as [[UNIX#UNIX-CLI standard commands]] and [[GNU]] commands, as well as external-downloaded binaries.
### /etc
System configuration files.
### /home
The home directory for each [[#Users|User]]. For example: ```/home/bmartin```. Generically, ```/home/<username> = ~```
### /opt
Additional software that is not part of the core system.
### /tmp
Temporary files that are created by applications and [[Linux Service|services]] running on the system.
### /usr
User-level programs, libraries, documentation and shared data files.
### /var
Contains logs files and system databases. It is called ```/var``` because they change frequently.
### /boot
Boot loader files and kernel images needed to start the system.
### /dev
Device files that represents internal hardware devices of the system: printers, terminals (`/dev/tty`), hard disks (```/dev/sda```)...
### /lib
Shared library files that are shared between programs.
### /media
Mount point for removable devices (USBs, CD-ROMs, external hard drives). When we insert a USB, it might be accessible through ```/media/usb```, for example.
### /mnt
Used to mount file systems temporarily, such as network file systems or disk images.
### /run
Temporary files created by [[Linux Service|system services]].
### /sys
System Hardware information and devices.
### .
Represents the current directory.
### ..
Represents the previous directory.
![[previous-dir-example.png]]

# APT
_(Only for Debian-based Linux Systems, such as Debian or Ubuntu), stands for Advanced Packaging Tool_
Like [[#RPM]], It is a tool for managing software packages, but in **Debian-based** Linux Systems. For example, you can install [[ElasticSearch]] software through APT.
- It manages package dependencies automatically
- It uses [[#Automatically downloading .deb package|APT repositories]]
- It allows user to automatically [[#Automatically downloading .deb package|download packages with just the package name]]
## Debian package .deb
It is a file with `.deb` extension and represents the software you want to install.
## Hands on
### Install a Debian package
#### Manually downloading .deb package
Download [[#Debian package .deb]] and run:
```shell
sudo apt install ./<package.deb>
```
You might need to set `644` permissions to package (`chmod 644 <package.deb>`): refer to [[GNU#chmod (Change file or directory permissions)]].
#### Automatically downloading .deb package
You can also download your Debian package automatically specifying the URL of the repository where to download from. A [[GNU#GPG public and private keys]] might be needed.
For this purpose, go to [[#/etc]]/apt/sources.list.d directory, then create a `.list` file and add a line with this format:
```
deb [signed-by=<path_to_gpg_key.gpg>] <repository_URL> stable main
```
Then, update package lists:
```shell
sudo apt update
```
And install the package:
```shell
sudo apt install <package_name>
```
#### List packages
```shell
sudo dpkg --list | grep <package_name>
```
#### Uninstall package
Remove everything, configuration files included:
```shell
sudo apt purge <package_name>
```
Remove only the package, but not configuration files:
```shell
sudo apt remove <package_name>
```
# RPM
_RPM stands for Red Hat Package Manager_
Like [[#APT]], It is a tool for managing software packages, but in **Red Hat-based** Linux Systems, like [[Distributions#Fedora|Fedora]] and [[Distributions#Red Hat Enterprise Linux (RHEL)|RHEL]].
## RPM file
It is a file with `.rpm` extension and represents the software you want to install.
- It does NOT manage package dependencies automatically
- It uses RPM repositories
- It does NOT allow user to automatically download a package: you must specify the `.rpm` file to install the software
## Hands on
### Install a rpm package
#### Manually downloading .rpm file
Download the `.rpm` file from the software official website and then run:
```shell
npm -i <file.rpm>
```
