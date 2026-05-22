
# Cybersecurity Detection & Monitoring Lab

# System Information

* CPU: 12th Gen Intel(R) Core(TM) i5-12400
- RAM: 16GB
- Storage: 256 GB M.2, 512 GB SSD, 1TB HDD
- Graphics Card: Radeon RX 560 (4 GB)
- Motherboard: Asus PRIME B760M-F
- Host Operating System: Microsoft Windows 10 Pro

# Network Diagram

![[Network_diag.png]]
# Splunk Installation
**Downloading Splunk Version : splunk-10.2.2**

![[splunk_download.png]]

**Installing Splunk**

After installing we get an error

![[splunk_create_error.png]]

Changed permission of **/opt/splunk** dir & sub dir to user **splunk** and group **splunk**

![[splunk_permission.png]] 

**Starting Splunk**

![[splunk_start.png]]
![[splunk_login_Page.png]]
![[splunk_home.png]]

-------------------------------------------------------------
**Download Universal Forwarder** [Link](https://www.splunk.com/en_us/download/universal-forwarder.html)

![[splunk_universal_down.png]]

**Installing Universal Forwarder**

![[splunk_ins1.png]]
![[splunk_ins2.png]]

Set Receiver Port on Kali Machine : **9997** 
![[splunk_ins3.png]]Get ip address of kali machine : **192.168.211.129**
![[splunk_ins4.png]]

Now set **Host: 192.168.211.129** and **Port: 8089** as **Deployment Server**  in Windows Machine 
![[splunk_ins5.png]]
![[splunk_ins6.png|590]]

Check if the forwarder is active 

![[splunk_chk_forwarder.png]]

Now, we will setup sysmon. First we will download and extract sysmon files to C:\Tools\Sysmon
![[Sysmon_down.png]]

Save the configuraion file to the same directory
![[sysmon_config.png]]

Installing Sysmon
![[Sysmon_install.png]]

Check if sysmon is running and logging
![[sysmon_chk.png]]

Now, Create a new index **win** from kali machine
![[Index.png]]

Now create a local app for Sysmon input at : `**C:\Program Files\SplunkUniversalForwarder\etc\apps\TA-local-sysmon\local\inputs.conf**`
![[Input_filter.png]]

Now restart the forwarder:
![[Restart_forwarder.png]]

Checking in kali machine for logs on newly created index = win

![[index_win.png]]

![[splunk_home_data.png]]

Enable useful Windows logging with powershell:

```
wevtutil sl "Microsoft-Windows-PowerShell/Operational" /e:true
wevtutil sl "Microsoft-Windows-TaskScheduler/Operational" /e:true
wevtutil sl "Microsoft-Windows-Windows Defender/Operational" /e:true
```


Install Splunk Add-on for Sysmon which will help use to check the logs properly. 
![[Sysmon_addon.png]]


# Install Atomic RED Team

```
[Net.ServicePointManager]::SecurityProtocol =[Net.SecurityProtocolType]::Tls12
Set-ExecutionPolicy Bypass -Scope Process -Force

Install-Module -Name invoke-atomicredteam,powershell-yaml -Scope CurrentUser -Force

IEX (IWR 'https://raw.githubusercontent.com/redcanaryco/invoke-atomicredteam/master/install-atomicredteam.ps1' -UseBasicParsing)
Install-AtomicRedTeam -getAtomics
Import-Module Invoke-AtomicRedTeam 
```

Sometime it will make error for directory, To solve this we need to set the environment path to fix the issue.
![[Invoke_error.png]]
```
```
```
Set-ExecutionPolicy Bypass -Scope Process -Force

Import-Module "C:\AtomicRedTeam\invoke-atomicredteam\Invoke-AtomicRedTeam.psd1" -Force
$PSDefaultParameterValues = @{
  "Invoke-AtomicTest:PathToAtomicsFolder" = "C:\AtomicRedTeam\atomics"
}

Get-Command Invoke-AtomicTest
````

![[invoke_fix.png]]

# T1053.005 (Persistence) | Scheduled Task: Create a task that runs a hidden script (every minute.)

Check of for modules of **T1053.005**
![[T1053.005.png]]
 The built-in test 8 -  T1053.005-8 Import XML Schedule Task with Hidden Attribute does **hidden** has similarity to the task.
 Execute and check if its ok
 ![[T1053.005-8_done.png]]

# T1218.005 (Defense Evasion) | MSHTA: Execute a malicious remote .hta file to bypass app control.

Check of modules of T1218.005
![[T1218.005.png]]As 3 is matches our requirements, we will use **T1218.005-3**
![[T1218.005-3-done.png]]

# T1003.001 (Credential Access) | LSASS Dumping: Use procdump to steal credentials from memory.

Check the module of **T1003.001**
![[T1003.001.png]]
As 1 matches our requirement, we will use **T1003.001-1**
For first run, windows defender blocked the process. So we had to disable the defender real-time protection then it worked and file has been written on **C:\Windows\Temp\lsass_dump.dmp**
![[T1003.001-1-done.png]]

# T1059.001 (Execution) | PowerShell Download:  Download and execute a script from the web.

Check the module of **T1059.001**
![[T1059.001.png]]
As option 6 and 8 both are good choice but 6 is purely on powershell. So we are using option 6.
![[T1059.001-done.png]]

# T1112 (Defense Evasion) | Registry Modification: Disable Windows Defender via

Check the modules of T1112 
![[T1112.png]]
For this task, we will use three sperate module. **T1112-38 (Suppress Win Defender Notifications),  T1112-51 (Disable Win Defender Notification) and T1112-56 (Tamper Win Defender Protection)**
![[T1112-done.png]]
**T1112-38 and T1112-51 completed successfully but T1112-56 was blocked by windows security but no notification has been popped because of other two options.**

# LOG Analysis

As we have installed **Splunk Add-on for Sysmon**, we need to do some changes to work with that addon.
App -> Manage App -> **Splunk Add-on for Sysmon**  -> Edit -> Visible (Yes)
![[sysmon_addon1.png]]
![[sysmon_addon-2.png]]
Now a new menu will show in apps section
![[sysmon_addon3.png]]

# The Detection (Blue Team)

**1. T1053.005 (Persistence) | Scheduled Task** : Sysmon Event ID 1 is most helpful because it can show `powershell.exe` being launched with scheduled-task registration behavior such as `Register-ScheduledTask`, `New-ScheduledTask`, or XML-based task import. This provides the required process name, command line, and time of execution.

```
index="win" ("Sysmon" OR "Microsoft-Windows-Sysmon") (EventCode=1 OR EventID=1)
(Image="*\\powershell.exe" OR Image="*\\pwsh.exe")
(
    CommandLine="*Register-ScheduledTask*"
    OR CommandLine="*New-ScheduledTask*"
    OR CommandLine="*Set-ScheduledTask*"
    OR CommandLine="*-Xml*"
    OR CommandLine="*ScheduledTask*"
    OR CommandLine="*TaskScheduler*"
)
| eval Time=strftime(_time,"%Y-%m-%d %H:%M:%S")
| eval Process_Name=mvindex(split(Image,"\\"),-1)
| eval Command_Line=CommandLine
| table Time host User EventCode EventID Process_Name Image Command_Line ParentImage ParentCommandLine ProcessId ParentProcessId
```

**Proof:**
![[blue_T1053.005.png]]


**2. T1218.005 (Defense Evasion) | MSHTA:** Sysmon Event ID 1 is most helpful because it shows `mshta.exe` being launched and captures the full command line, including the `.hta` payload.

```
index=win ("Sysmon" OR "Microsoft-Windows-Sysmon") (EventCode=1 OR EventID=1)
(Image="*\\mshta.exe" OR OriginalFileName="MSHTA.EXE")
(
    CommandLine="*http://*"
    OR CommandLine="*https://*"
    OR CommandLine="*.hta*"
    OR CommandLine="*vbscript:*"
    OR CommandLine="*javascript:*"
)
| eval Time=strftime(_time,"%Y-%m-%d %H:%M:%S")
| eval Process_Name=mvindex(split(Image,"\\"),-1)
| eval Command_Line=CommandLine
| table Time host User EventCode EventID Process_Name Image Command_Line ParentImage ParentCommandLine ProcessId ParentProcessId
```
**Proof:**
![[blue_T1218.005.png]]


**3. T1003.001 (Credential Access) | LSASS Dumping:** Sysmon Event ID 10 is most helpful because it shows a process accessing lsass.exe. That is the key behavior for LSASS credential dumping. The join with Sysmon Event ID 1 adds the original process command line, which proves the process name, command line, and time.


```
index=win ("Sysmon" OR "Microsoft-Windows-Sysmon") (EventCode=10 OR EventID=10)
TargetImage="*\\lsass.exe"
(
    SourceImage="*\\procdump.exe"
    OR SourceImage="*\\procdump64.exe"
    OR GrantedAccess="0x1fffff"
    OR GrantedAccess="0x1010"
    OR GrantedAccess="0x1410"
    OR GrantedAccess="0x143a"
)
| eval Access_Time=strftime(_time,"%Y-%m-%d %H:%M:%S")
| eval ProcessGuid=coalesce(SourceProcessGuid, SourceProcessGUID, source_process_guid)
| eval Event10_Process_Path=coalesce(SourceImage, source_image)
| eval Event10_Process_Name=mvindex(split(Event10_Process_Path,"\\"),-1)
| join type=left ProcessGuid [
    search index=win ("Sysmon" OR "Microsoft-Windows-Sysmon") (EventCode=1 OR EventID=1)
    | eval ProcessGuid=coalesce(ProcessGuid, process_guid)
    | eval Created_Time=strftime(_time,"%Y-%m-%d %H:%M:%S")
    | eval Created_Process_Name=mvindex(split(Image,"\\"),-1)
    | rename Image as Created_Image
    | rename CommandLine as Created_CommandLine
    | table ProcessGuid Created_Time Created_Process_Name Created_Image Created_CommandLine ParentImage ParentCommandLine ProcessId ParentProcessId
]
| eval Time=coalesce(Created_Time, Access_Time)
| eval Process_Name=coalesce(Created_Process_Name, Event10_Process_Name)
| eval Image=coalesce(Created_Image, Event10_Process_Path)
| eval Command_Line=coalesce(Created_CommandLine, CommandLine, "Command line not stored in Event ID 10; correlated with Event ID 1 if available")
| table Time host User EventCode EventID Process_Name Image Command_Line SourceImage TargetImage GrantedAccess CallTrace ParentImage ParentCommandLine ProcessId ParentProcessId ProcessGuid
```
**Proof:**
![[blue_T1003.001.png]]


**4. T1059.001 (Execution) | PowerShell Download:** Sysmon Event ID 1 is most helpful because it records `powershell.exe` execution and captures the command line showing web download behavior and execution behavior.

```
index=win ("Sysmon" OR "Microsoft-Windows-Sysmon") (EventCode=1 OR EventID=1)
(Image="*\\powershell.exe" OR Image="*\\pwsh.exe")
(
    CommandLine="*http://*"
    OR CommandLine="*https://*"
    OR CommandLine="*Invoke-WebRequest*"
    OR CommandLine="*iwr*"
    OR CommandLine="*wget*"
    OR CommandLine="*curl*"
    OR CommandLine="*DownloadString*"
    OR CommandLine="*DownloadFile*"
    OR CommandLine="*Net.WebClient*"
    OR CommandLine="*System.Net.WebClient*"
)
(
    CommandLine="*iex*"
    OR CommandLine="*Invoke-Expression*"
    OR CommandLine="*-enc*"
    OR CommandLine="*-EncodedCommand*"
    OR CommandLine="*-ExecutionPolicy Bypass*"
    OR CommandLine="*-nop*"
    OR CommandLine="*-NoProfile*"
)
| eval Time=strftime(_time,"%Y-%m-%d %H:%M:%S")
| eval Process_Name=mvindex(split(Image,"\\"),-1)
| eval Command_Line=CommandLine
| table Time host User EventCode EventID Process_Name Image Command_Line ParentImage ParentCommandLine ProcessId ParentProcessId
```
**Proof:**
![[blue_T1059.001.png]]


**5. T1112 (Defense Evasion) | Registry Modification:** Sysmon Event ID 1 was the most helpful in this environment because it captured the process responsible for the Defender modification attempt, such as `reg.exe`. The command line showed Defender-related modification behavior, including values such as `TamperProtection`, `DisableNotifications`, `Set-MpPreference`, or related Defender settings. This provided the required proof of the process name, command line, and time of the attack.


```
index=win ("Sysmon" OR "Microsoft-Windows-Sysmon") (EventCode=1 OR EventID=1)
(
    Image="*\\reg.exe"
    OR Image="*\\powershell.exe"
    OR Image="*\\pwsh.exe"
)
(
    CommandLine="*Windows Defender*"
    OR CommandLine="*DisableAntiSpyware*"
    OR CommandLine="*TamperProtection*"
    OR CommandLine="*DisableNotifications*" 
    OR CommandLine="*Notification_Suppress*" 
    OR CommandLine="*DisableRealtimeMonitoring*"
    OR CommandLine="*DisableBehaviorMonitoring*"
    OR CommandLine="*DisableOnAccessProtection*"
    OR CommandLine="*Set-MpPreference*"
    OR CommandLine="*Add-MpPreference*"
)
| eval Time=strftime(_time,"%Y-%m-%d %H:%M:%S")
| eval Process_Name=mvindex(split(Image,"\\"),-1)
| eval Command_Line=CommandLine
| table Time host User EventCode EventID Process_Name Image Command_Line ParentImage ParentCommandLine ProcessId ParentProcessId
```
**Proof:**
![[blue_T1112.png]]