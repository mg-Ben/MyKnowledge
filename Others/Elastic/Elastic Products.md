---
tags:
  - Others
---
# Core principles

## Support Matrix
_Refer to [Support Matrix | Elastic](https://www.elastic.co/es/support/matrix)_
### Windows Server 32 bits 2003 support
#### Beats
##### MetricBeat
If you want to download and install [[Beats#MetricBeat|MetricBeat]] in [[Windows#Distributions#Windows Server 2003]], the most recent version is `6.4.3`. To test it:
- Create a [[Virtualization#Guest Operating System|Guest OS]] Virtual Machine with [[Virtualization#VMware|VMware]] or any other virtualization tool. You can find the ISO [[Windows#Download Windows ISO#Windows Server 2003|here]]
- In your [[Virtualization#Host Operating System|Host OS]], [[Beats#MetricBeat#Download MetricBeat package|download the MetricBeat .zip file for Windows 32 bits]]
- To pass the `.zip` to your Guest OS, create a [[Virtualization#Shared folders|Shared folder]] between your Host and Guest OS and place the installed zip there
- Access to Guest OS, unzip the `.zip` package and [[Beats#Compatibility check#On Windows|perform a compatibility check]]. If you download different versions of MetricBeat, you will notice that the most recent one such that is compatible with Windows Server 32 bits 2003 is `6.4.3`