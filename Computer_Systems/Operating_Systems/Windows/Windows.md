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
