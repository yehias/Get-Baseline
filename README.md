# Get-Baseline

Get-Baseline is a wrapper PowerShell script for a number of functions that automates the initial tasks in an incident response scenario. 

#### `Get-Baseline`

Primary function. Calls all Remote Access, Baseline Collection, and EventLog Collection functions. 

## Baseline Collection

**Collect ASEPs, live process, configuration data for systems in scope.**

#### `Get-BetterTasklist`

Collects current running processes - TimeGenerated, Name, ProcessID, ParentProcessId, ExecutablePath, Hash, CommandLine

#### `Get-DLLs`

Collects current loaded DLLs - TimeGenerated, ModuleName, FileName, Hash, Size, Company, Description, FileVersion, Product, ProductVersion

#### `Get-BetterNetstat`

Collects current netstat output - TimeGenerated, Protocol, LocalAddress, ForeignAddress, State, Name, ProcessId, ParentProcessId, ExecutablePath, Hash, CommandLine 

#### `Invoke-Autorunsc`

Download and execute autorunsc.exe with the following arguments: -accepteula -h -c -nobanner -a * -s

#### `Invoke-Sigcheck`

Verifies signature integrity on the system based on Matt Graeber's "Subverting Trust in Windows" then downloads and executes sigcheck.exe -accepteula -c -u -e -s -r -nobanner C:\Windows\System32 and C:\Windows\SysWOW64

#### `Get-AuditOptions`

Checks registry for additional auditing options - Process Creation Command Line, PowerShell Transcription, PowerShell Script Block Logging, PowerShell Module Logging, Windows Event Forwarding.


## Remote Access

**Enable PSRemoting for systems in scope.**

#### `Enable-RemoteAccess`

Enable PowerShell Remoting / WinRM via SMB (PsExec) or WMI (Invoke-WmiMethod)

#### `Enable-WinRMPsExec`

Enable PowerShell Remoting / WinRM via SMB (PsExec)

#### `Enable-WinRMWMI`

Enable PowerShell Remoting / WinRM via WMI (Invoke-WmiMethod)


## Event Log Collection

**Collect security-relevant event logs for input into SIEM.**

#### `Get-HuntData`

Collects Windows Event Log data from the following Logs:
* Application
* System
* Security
* Windows PowerShell
* Microsoft-Windows-Windows Defender/Operational
* Microsoft-Windows-AppLocker/EXE and DLL
* Microsoft-Windows-AppLocker/MSI and Script
* Microsoft-Windows-AppLocker/Packaged app-Execution
* Microsoft-Windows-DeviceGuard/Operational
* Microsoft-Windows-PowerShell/Operational
* Microsoft-Windows-Windows Firewall With Advanced Security/Firewall
* Microsoft-Windows-Sysmon/Operational

## Prerequisites

On Targets:
* Accessible via WinRM, SMB, or WMI (WinRM Preferred)
* PowerShell 2.0+ (3.5+ Preferred)

On Collection System:  
* PowerShell 5.0+
* Domain Joined
* Logged in with rights as Administrator (able to execute code remotely)


## Usage
### Execution
```
PS> Get-Baseline -Targets dc01,srv01,srv02,pc02win10 -url "http://10.0.0.128:8080/"
```

```
PS> Get-Baseline -Targets $(get-content <IP_list_file.txt>) -url "http://10.0.0.128:8080/" -SkipSigcheck
```

### Output
```
PS C:\Users\Administrator\20171212_Survey> Get-Baseline -Targets dc01,srv01,srv02,pc02win10 -url "http://10.0.0.128:8080/" -Verbose
Transcript started, output file is .\Log_20171212.txt

    Directory: C:\Users\Administrator\20171212_Survey

Mode                LastWriteTime     Length Name
----                -------------     ------ ----
d----        12/12/2017   9:25 PM            Baseline


If WinRM/PSRemoting is DISABLED, attempt to ENABLE with PsExec? [y/n]: y

If WinRM/PSRemoting and SMB is DISABLED, attempt to ENABLE with WMI? [y/n]: y
VERBOSE: Testing Remote Management Options for dc01
VERBOSE: PSRemoting Enabled on dc01
VERBOSE: Testing Remote Management Options for srv01
VERBOSE: SMB Enabled on srv01
VERBOSE: Testing Remote Management Options for srv02
VERBOSE: WMI Enabled on srv02
VERBOSE: Testing Remote Management Options for pc02win10
VERBOSE: PSRemoting Enabled on pc02win10

========================================================================
Pre-Execution Report

PowerShell Remoting Targets:
dc01 pc02win10

SMB/PsExec Remoting Targets:
srv01

WMI Remoting Targets:
srv02

Targets with NO REMOTING Options:


========================================================================

You have elected to enable PSRemoting via PsExec.
You have elected to enable PSRemoting via WMI.

Are you sure you want to execute? [y/n]: y
VERBOSE: Executing PsExec...
VERBOSE: Executing winrm quickconfig -q on srv01 with PsExec
VERBOSE: Success enabling PSRemoting on srv01 with PsExec
VERBOSE: Executing WMI...
VERBOSE: Executing winrm quickconfig -q on srv02 with WMI
VERBOSE: Success enabling PSRemoting on srv02 with WMI

========================================================================
Post-Execution Report

PowerShell Remoting Targets:
dc01 pc02win10


SMB/PsExec Remoting Targets SUCCESS enabling PSRemoting:
srv01

SMB/PsExec Remoting Targets FAILED enabling PSRemoting:



WMI Remoting Targets SUCCESS enabling PSRemoting:
srv02

WMI Remoting Targets FAILED enabling PSRemoting:



Targets with NO REMOTING Options:



FINAL Targets ready for PSRemoting:
dc01 pc02win10 srv01 srv02
========================================================================

Scheduled to execute baseline collection on:
dc01 pc02win10 srv01 srv02

Are you sure you want to execute? [y/n]: y
VERBOSE: Getting Audit Levels
VERBOSE: Getting Additional Audit Options
VERBOSE: Getting System Information
VERBOSE: Getting Better Tasklist
VERBOSE: Getting Loaded DLLs
VERBOSE: Getting Better TCP Netstat
VERBOSE: Getting Better TCPv6 Netstat
VERBOSE: Getting Better UDP Netstat
VERBOSE: Getting Better UDPv6 Netstat
VERBOSE: Getting Autorunsc Data
VERBOSE: Checking System32 and SysWOW64 for unsigned binaries
VERBOSE: Getting Event Log Settings
d----        12/12/2017   9:38 PM            EventLogData
VERBOSE: Collecting Application Log
VERBOSE: Collecting System Log
VERBOSE: Collecting Powershell Log
VERBOSE: Collecting Microsoft-Windows-Windows Defender/Operational
VERBOSE: Collecting Microsoft-Windows-AppLocker/EXE and DLL
VERBOSE: Collecting Microsoft-Windows-AppLocker/MSI and Script
VERBOSE: Collecting Microsoft-Windows-AppLocker/Packaged app-Execution
VERBOSE: Collecting Microsoft-Windows-DeviceGuard/Operational
VERBOSE: Collecting Microsoft-Windows-PowerShell/Operational
VERBOSE: Collecting Microsoft-Windows-Windows Firewall With Advanced Security/Firewall
VERBOSE: Collecting Microsoft-Windows-Sysmon/Operational
VERBOSE: Collecting Security Log on dc01
VERBOSE: Collecting Security Log on pc02win10
VERBOSE: Collecting Security Log on srv01
VERBOSE: Collecting Security Log on srv02
Transcript stopped, output file is C:\Users\Administrator\20171212_Survey\Log_20171212.txt

PS C:\Users\Administrator\20171212_Survey>

```
### Results
```
PS C:\Users\Administrator\20171212_Survey> Get-ChildItem -Recurse

    Directory: C:\Users\Administrator\20171212_Survey

Mode                LastWriteTime     Length Name
----                -------------     ------ ----
d----        12/12/2017   9:38 PM            Baseline
d----        12/12/2017   9:39 PM            EventLogData
-a---        12/12/2017   9:48 PM      32034 Log_20171212.txt
-a---        12/12/2017   9:28 PM     339096 PsExec.exe

    Directory: C:\Users\Administrator\20171212_Survey\Baseline

Mode                LastWriteTime     Length Name
----                -------------     ------ ----
-a---        12/12/2017   9:31 PM       4140 auditoptions.csv
-a---        12/12/2017   9:31 PM      34754 auditpol.csv
-a---        12/12/2017   9:34 PM    2481407 autorunsc.csv
-a---        12/12/2017   9:31 PM     838746 dlls.csv
-a---        12/12/2017   9:38 PM       5031 eventloglist.csv
-a---        12/12/2017   9:32 PM      35431 netstat_TCP.csv
-a---        12/12/2017   9:32 PM      23498 netstat_TCPv6.csv
-a---        12/12/2017   9:33 PM     500623 netstat_UDP.csv
-a---        12/12/2017   9:33 PM       6172 netstat_UDPv6.csv
-a---        12/12/2017   9:38 PM      36450 sigcheck.csv
-a---        12/12/2017   9:31 PM       5063 systeminfo.csv
-a---        12/12/2017   9:31 PM      63379 tasklist.csv

    Directory: C:\Users\Administrator\20171212_Survey\EventLogData

Mode                LastWriteTime     Length Name
----                -------------     ------ ----
-a---        12/12/2017   9:39 PM    6861041 eventlog_application.csv
-a---        12/12/2017   9:39 PM          0 eventlog_applocker_exedll.csv
-a---        12/12/2017   9:39 PM          0 eventlog_applocker_msiscript.csv
-a---        12/12/2017   9:39 PM          0 eventlog_applocker_packaged.csv
-a---        12/12/2017   9:39 PM     282881 eventlog_defender_operational.csv
-a---        12/12/2017   9:39 PM     108447 eventlog_deviceguard_operational.csv
-a---        12/12/2017   9:41 PM    1969917 eventlog_firewall.csv
-a---        12/12/2017   9:39 PM    5775288 eventlog_powershell.csv
-a---        12/12/2017   9:41 PM    9907825 eventlog_powershell_operational.csv
-a---        12/12/2017   9:42 PM  119451056 eventlog_security_dc01.csv
-a---        12/12/2017   9:47 PM  285187744 eventlog_security_pc02win10.csv
-a---        12/12/2017   9:48 PM  109548498 eventlog_security_srv01.csv
-a---        12/12/2017   9:48 PM    8306071 eventlog_security_srv02.csv
-a---        12/12/2017   9:41 PM          0 eventlog_sysmon_operational.csv
-a---        12/12/2017   9:39 PM    9043921 eventlog_system.csv
```
### SIEM
props.conf
```
[csv_eventlog]
DATETIME_CONFIG = 
INDEXED_EXTRACTIONS = csv
KV_MODE = none
NO_BINARY_CHECK = true
SHOULD_LINEMERGE = false
TIMESTAMP_FIELDS = TimeGenerated
category = Structured
description = Comma-separated value format. Set header and other settings in "Delimited Settings"
disabled = false
pulldown_type = true
REPORT-Message = message_delims
```
transforms.conf
```
[message_delims]
SOURCE_KEY = Message
DELIMS = "\n", "=:"
CLEAN_KEYS = true
MV_ADD     = true
```

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details

## Acknowledgments
