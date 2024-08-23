---
tags:
  - Computer_Systems
  - Operating_Systems
---
# Core principles
It is a [[UNIX|UNIX-like]] [[Operating System]].
Linux is the [[Operating System#Kernel|Kernel]].
## Concepts
### cgroups
A Linux Kernel feature that allows us to allocate resources such as CPU time, memory, network bandwith... For a group of processes. It prevents processes from interfering one another and consuming excessive resources by limiting them.
To do so, cgroups organizes the processes using some hierarchy (like tree structures). Each hierarchy group has got its own constraint of resources. Each cgroup would be a node in the tree structure, and the resources limit applied to some node is applied automatically to its children cgroups.
The directory to control the cgroup is `/sys/fs/cgroup`.
[[Linux Service#systemd]] can be used to manage cgroups easily. For example, you can limit the CPU usage of some [[Linux Service]] adding a setting inside its [[Linux Service#Unit file#Example template|unit file]] (such as `CPUQuota=50%` to limit the CPU usage to 50%).
### LVM
_LVM stands for Logical Volume Manager_
A feature in Linux that allows flexible disk management:
- Resizing of [[partitions]]
- Snapshots
- Pooling of multiple physical volumes into a single logical volume group
#### PV
_PV stands for Physical Volume_
In [[GNU]]/[[Linux]] [[Operating System|Operating Systems]], we can also initialize a partition (or even the entire disk) as a **PV** (Physical Volume). This means that we can use that partition to be grouped with other PVs so as to create a single [[#VG|VG (Volume Group)]].
For example, we can initialize `/dev/sda2` and `/dev/sdb1` as PVs to be combined into a Volume Group.
#### VG
_VG stands for Volume Group_
The combination of one or more [[#PV|PVs]]. It aggregates the storage capacity of all its PVs into a single, large pool of storage.
For example, you can combine `/dev/sda2` and `/dev/sdb1` in a Volume Group.
##### LV
_LV stands for Logical Volume_
Within a [[#VG]] we can create **LVs** (Logical Volumes).
- A LV acts like a [[#PV]] with the difference that they are not physical, so you we resize it whenever we want
- You can even configure the [[Operating System#File system|File system]] type of the LV: for example, LV1 can be EXT4, LV2 can be NTFS, LV3 can be EXT4...)
Like [[Operating System#Partition|partitions]], each LV has got a mount point. When we mount the LV in the unified [[#File system]], the data for that LV will be accessible from the directory we have mounted the partition (i.e. _Mount point_).
For example, we can mount `LV1` inside the directory `/mnt/volume1` and `LV2` inside `/mnt/volume2`.
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
You may be prompted to enter a password. In that case, you can try with:
```shell
sudo -su <username>
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
Contains essential [[BinaryFile|Binary files]] such as [[UNIX#UNIX-CLI standard commands]] and [[GNU]] commands, as well as external-downloaded binaries. These binaries are required for the system to boot and operate correctly.
### /sbin
_System Binaries_
Contains essential [[BinaryFile|Binary files]] such as [[UNIX#UNIX-CLI standard commands]] and [[GNU]] commands, as well as external-downloaded binaries. These binaries are required for the system to boot and operate correctly.
However, these binaries are used by system administrators for system maintenance and configurations.
Examples: `ifconfig`, `shutdown`, `reboot`...
### /etc
System configuration files.
### /home
The home directory for each [[#Users|User]].
#### /home/username
The user home.
For example: `/home/bmartin`. Generically, ```/home/<username> = ~```
### /opt
Additional software that is not part of the core system.
### /tmp
Temporary files that are created by applications and [[Linux Service|services]] running on the system.
### /usr
User-level programs, libraries, documentation and shared data files.
#### /usr/bin
Contains the majority of user commands ([[BinaryFile|Binary files]]) and application binaries that are not required for system boot or repair but are used for regular operations and applications.
**Examples:** [[GNU#grep (Search for patterns in files or command output)|grep]], [[GNU#find (Search for files in a directory hierarchy)|find]], [[AWK#Hands on|awk]], [[Python#Hands on#Run python Script|python]]...
#### /usr/local/bin
Contains software and scripts (i.e. [[BinaryFile|Binary files]]) installed locally by system administrator or user, such as custom scripts, third-party software, local compiled programs...
### /var
Contains logs files and system databases. It is called ```/var``` because they change frequently.
### /boot
Boot loader files and kernel images needed to start the system.
### /dev
Device files that represents internal hardware devices of the system: printers, terminals (`/dev/tty`), hard disks (```/dev/sda```)...
For instance: `/dev/sda1` means:
- `sd` stands for [[Peripherial Devices#SCSI|SCSI]] disk
- `a` is the first detected disk, `b` the second and so on
	- `1` is the first partition inside that disk
	- `2` is the second partition inside that disk
	- `3` is the third partition inside that disk...
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
### /proc
_Virtual file system: there are no real data on disk_
Information about processes in the system and other system data.
#### HardIRQ (or IRQ)
You can get Hardware [[Operating System#IRQ Time|IRQ]] information from `/proc/interrupts` file
#### SoftIRQ
You can get Software [[Operating System#IRQ Time|IRQ]] information from `/proc/softirqs` file
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
You can also download your Debian package automatically specifying the URL of the repository where to download from. A [[GNU#GPG public and private keys]] might be needed. That `.gpg` key is often downloaded from the official website of the software and the path where to save it is usually `/etc/apt/keyrings`.
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
Some packages don't require setting the repository URL and can be download just by `sudo apt install <package_name>`.
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
Like [[#APT]], It is a tool for managing software packages in **low-level**, but in **Red Hat-based** Linux Systems, like [[Distributions#Fedora|Fedora]] and [[Distributions#Red Hat Enterprise Linux (RHEL)|RHEL]].
## RPM file
It is a file with `.rpm` extension and represents the software you want to install.
- It does NOT manage package dependencies automatically
- It uses RPM repositories
- It does NOT allow user to automatically download a package: you must specify the `.rpm` file to install the software
## Hands on
### List rpm packages
```shell
rpm -qa
```
### Install a rpm package
#### After manually downloading .rpm file
Download the `.rpm` file from the software official website and then run:
```shell
rpm -i <file.rpm>
```
### Get rpm package information
_Whenever you want to get package information, use `-qp` flag (which stands for `query-package`)_
#### Extract .rpm scripts
```shell
rpm -qp --scripts <file.rpm>
```
# YUM
_YUM stands for Yellowdog Updater, Modified_
It is a **high-level** software management tool that resolves dependencies automatically.
# DNF
_Reference: [Managing software with the DNF tool Red Hat Enterprise Linux 9 | Red Hat Customer Portal](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/9/html-single/managing_software_with_the_dnf_tool/index#proc_setting-yum-repository-options_assembly_managing-custom-software-repositories)_
_DNF stands for DaNdified YUM_
Like [[#RPM]], DNF is a management tool for managing software in **Red Hat-based** Linux Systems such as [[Distributions#Fedora|Fedora]] and [[Distributions#Red Hat Enterprise Linux (RHEL)|RHEL]]. The difference is that DNF manages software at **high-level**.
## Hands on
### Configure a repository
Go to either:
- `/etc/yum.repos.d/<file.repo>`
- `/etc/dnf/dnf.conf`

### List installed packages
```shell
dnf list --installed
```
### Get package dependencies
```shell
dnf repoquery --requires <package-name>
```
Would return the package dependencies of `<package-name>`.
### Download packages but not installing
You can get the `.rpm` package by:
```shell
dnf install --downloadonly --downloaddir /path <package_name1> <package_name2>...
```
### Redownload installed packages but not installing
You can get the `.rpm` package by:
```shell
dnf reinstall --downloadonly --downloaddir /path <package_name1> <package_name2>...
```
#### Example: get metricbeat .rpm package
```shell
dnf reinstall --downloadonly --downloaddir /tmp metricbeat --releasever 7.5.2
```
The installed `.rpm` packages will be inside `/tmp` folder.