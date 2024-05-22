---
tags:
  - Computer_Systems
  - Operating_Systems
---
# Core principles
## CMD
The [[Operating System#Shell|Shell CLI]] (_Command Line Interface_).
## PowerShell
The [[Operating System#Shell|Shell CLI]] (_Command Line Interface_).
### Profile file
The profile file for PowerShell is a `.ps1` Script file that contains commands and configurations that are automatically executed when you start a PowerShell session. You can:
- Define functions and variables
- Provide access to directories
- Set Script execution policies
- Configure environment variables
The path of the Script is stored in `$profile` environment variable:
```PowerShell
echo $profile
```
You can edit it with:
```PowerShell
notepad $profile
```
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
### CMD
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
