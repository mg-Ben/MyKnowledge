---
tags:
  - Computer_Systems
  - Operating_Systems
---
# Core principles
## IIS
_IIS stands for Internet Information Service_
It is a Microsoft **web server** (a set of services for Windows [[Operating System|Operating Systems]]).
## CMD
The [[Operating System#Shell|Shell CLI]] (_Command Line Interface_).
## PowerShell
The [[Operating System#Shell|Shell CLI]] (_Command Line Interface_).
### Profile file
The profile file for PowerShell is a `.ps1` Script file that contains commands and configurations that are automatically executed when you start a PowerShell session. You can:
- Run [[#Useful commands#PowerShell|PowerShell commands]] when executing PowerShell
- Define functions and variables
- Provide access to directories
- [[#Set Script execution policies]]
- Configure environment variables

The path of the Script is stored in `$profile` environment variable:
```PowerShell
echo $profile
```
You can edit it with:
```PowerShell
notepad $profile
```
#### Execution Policies
Windows PowerShell has got a feature to prevent PowerShell from running untrusted Scripts (from untrusted publishers). You can change this by modifying the **Execution Policy**.
##### Get current Script execution policy
```PowerShell
Get-ExecutionPolicy
```
##### Set Script execution policies
Once you have edited your `.ps1` Script, when opening a new PowerShell your Script will run automatically. However, you may find some errors related to execution policies, which means that Scripts form untrusted authors are not allowed to be executed in your PowerShell. To solve this:
1. Open PowerShell as administrator
2. Run:
```PowerShell
Set-ExecutionPolicy RemoteSigned
```
To allow running Scripts from anyone:
```PowerShell
Set-ExecutionPolicy Unrestricted
```
## WMI
_WMI stands for Windows Management Instrumentation_
It is what [[Beats]] use in the background to retrieve Windows System information.
WMI is a standarized way to query system, devices and applications information, and even configuring system settings.
### WMIC
_WMIC stands for [[#WMI]] Command-Line_
It is a **CLI** (Command Line Interface) to execute WMI queries.
## Task Manager
## Resource monitor
A built-in tool which shows performance metrics of [[Operating System#CPU|CPU]], [[Operating System#Main memory (RAM)|RAM]], [[Operating System#Secondary memory (Disk)|Disk]] and Network (i.e. resources of Windows).

![[windows-resmon.png]]
Open it with [[#resmon]] command.
# Distributions
## Windows Server 2003
### Directory layout
#### Desktop path
```
"C:\\Documents and Settings\\Administrator\\Desktop"
```
# Hands on
## Download Windows ISO
### Windows Server 2003
If you want to make tests with **Windows Server 2003** [[Operating System]], you can download it from [Windows Server 2003 Standard Edition RTM : Microsoft : Free Download, Borrow, and Streaming : Internet Archive](https://archive.org/details/en_windows_server_2003_standard). The product key should be as a comment in that webpage (`JB88F-WT2Q3-DPXTT-Y8GHG-7YYQY`).
## Useful commands
### PowerShell
#### Move to directory
```PowerShell
Set-Location <directory>
```
#### Get pwsh.exe location
```PowerShell
Get-Command pwsh | Select-Object -ExpandProperty Source
```
### CMD
#### Get system information
```cmd
systeminfo
```
#### Run .exe file
##### Windows Server 2003
```cmd
start <file.exe>
```
##### General Windows
```cmd
.\<file.exe>
```
#### Print file content
##### Windows Server 2003
```cmd
type <filename>
```
#### Print the last lines in real time of some file
##### General Windows
```cmd
Get-Content -Path path\\to\\file -Wait
```
#### resmon
Opens [[#Resource monitor]]:
```cmd
resmon
```
### WMIC
#### Start wmic
```PowerShell
wmic
```
#### Get wmic aliases
Either run:
```PowerShell
wmic alias list brief
```
Outside [[#WMIC]], or run:
```WMIC
/?
```
Inside [[#WMIC]].
#### Get data
Run:
```PowerShell
wmic path <alias> <other_options (optional)>
```
Outside [[#WMIC]].
Example:
- `<alias> = win32_perfrawdata_perfdisk_logicaldisk`
- `<other_options> = get <metric_name>`
- `<metric_name> = FreeMegabytes`
Or run:
```WMIC
<ALIAS>
```
Inside [[#WMIC]].
## Configuring **Internet** connectivity
### Virtualized Windows Server 2003
[[Virtualization#Bridge mode|Configure Virtual Machine in bridge mode]].
Assign an [[IP|IP address]] to your Windows Server 2003 VM; inside Windows Server 2003:
1. Press _Windows_ icon
2. Go to _Show all connections_
![[show-all-connections-menu.png]]
3. Right click on _LAN or High-Speed Internet_ and choose _Properties_
![[network-connections-menu.png]]
4. Select _Internet Protocol (TCP/IP)_ and click on _Properties_
![[LAN-properties.png]]
5. Assign an IP inside your [[Virtualization#Hyper-V LAN]] and set the Hyper-V LAN subnet mask

## Configure [[Operating System#Virtual memory (swap memory/space)|virtual memory]]
In Windows, virtual memory is called **Pagefile**, and usually located at `C:\\pagefile.sys`.
In order to change swap space, go to:
_Windows button > View advanced system settings > Advanced tab > Performance settings > Advanced tab > Change_
Once the changes are applied, press **Set button**.