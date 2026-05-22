
# Cybersecurity Detection & Monitoring Lab

# System Information

* CPU: 12th Gen Intel(R) Core(TM) i5-12400
- RAM: 16GB
- Storage: 256 GB M.2, 512 GB SSD, 1TB HDD
- Graphics Card: Radeon RX 560 (4 GB)
- Motherboard: Asus PRIME B760M-F
- Host Operating System: Microsoft Windows 10 Pro

# Network Diagram

<img width="660" height="371" alt="Network_diag" src="https://github.com/user-attachments/assets/eed4b65d-c3ad-4971-ad58-066f77c6cf0c" />

# Splunk Installation

**Downloading Splunk Version : splunk-10.2.2**

<img width="1278" height="228" alt="splunk_download" src="https://github.com/user-attachments/assets/7094a80a-a859-40fe-989b-576631236043" />

**Installing Splunk**

<img width="836" height="590" alt="splunk_create_error" src="https://github.com/user-attachments/assets/4f5c915b-d7f5-46ab-9986-d22f461439fa" />

After installing we get an error

<img width="836" height="590" alt="splunk_create_error" src="https://github.com/user-attachments/assets/0733cf2d-a5d0-4061-a415-bc91ce1df3fb" />

Changed permission of **/opt/splunk** dir & sub dir to user **splunk** and group **splunk**

<img width="635" height="52" alt="splunk_permission" src="https://github.com/user-attachments/assets/eeb62376-a75d-42a4-89af-a2491cb4b913" />

**Starting Splunk**

<img width="973" height="595" alt="splunk_start" src="https://github.com/user-attachments/assets/42674fa0-78b3-4dcf-9b70-21f05f855557" />
<img width="1292" height="799" alt="splunk_login_Page" src="https://github.com/user-attachments/assets/23ff3b65-a216-47ea-a811-d1d343c51cba" />
<img width="1294" height="801" alt="splunk_home" src="https://github.com/user-attachments/assets/138ce2a8-e942-4770-8cf1-85868e5c2296" />

-------------------------------------------------------------
**Download Universal Forwarder** [Link](https://www.splunk.com/en_us/download/universal-forwarder.html)

<img width="1330" height="606" alt="splunk_universal_down" src="https://github.com/user-attachments/assets/9755e42b-f555-4263-a994-8a28e0c8990d" />

**Installing Universal Forwarder**

<img width="579" height="454" alt="splunk_ins1" src="https://github.com/user-attachments/assets/ef1e373a-d293-49de-ba42-1930008e5974" />
<img width="586" height="466" alt="splunk_ins2" src="https://github.com/user-attachments/assets/3146e319-a395-4bc1-b0f2-24633a2cbfce" />

Set Receiver Port on Kali Machine : **9997** 
<img width="948" height="380" alt="splunk_ins3" src="https://github.com/user-attachments/assets/f96abb90-3e2e-4bc8-b815-e67e65ad8a50" />
Get ip address of kali machine : **192.168.211.129**
<img width="669" height="351" alt="splunk_ins4" src="https://github.com/user-attachments/assets/822ce5d0-efef-4544-a785-dd8d53ab43cf" />

Now set **Host: 192.168.211.129** and **Port: 8089** as **Deployment Server**  in Windows Machine 
<img width="577" height="486" alt="splunk_ins5" src="https://github.com/user-attachments/assets/f9b4e216-b636-4f99-83da-d1fbfd315293" />
<img width="610" height="495" alt="splunk_ins6" src="https://github.com/user-attachments/assets/f587609f-1ccd-4efd-821d-5c5305de5abc" />

Check if the forwarder is active 

<img width="861" height="603" alt="splunk_chk_forwarder" src="https://github.com/user-attachments/assets/afe5ceff-c98b-44f4-b073-4251c53b1b11" />

Now, we will setup sysmon. First we will download and extract sysmon files to C:\Tools\Sysmon
<img width="714" height="306" alt="Sysmon_down" src="https://github.com/user-attachments/assets/a6a469a6-a052-42cd-ad29-35eb19f2d9a5" />

Save the configuraion file to the same directory
<img width="831" height="383" alt="sysmon_config" src="https://github.com/user-attachments/assets/6a351aec-73f4-4ac3-bb2b-d4a2e984229d" />

Installing Sysmon
<img width="821" height="473" alt="Sysmon_install" src="https://github.com/user-attachments/assets/d5fed800-f618-49ec-87b1-f6fffae7cf58" />

Check if sysmon is running and logging
<img width="790" height="398" alt="sysmon_chk" src="https://github.com/user-attachments/assets/d7e6dff6-eed0-4606-b9e5-37e3ea7cccd0" />

Now, Create a new index **win** from kali machine
<img width="656" height="449" alt="Index" src="https://github.com/user-attachments/assets/724708f6-2b43-4448-b9a0-73019d7b7b9a" />

Now create a local app for Sysmon input at : `**C:\Program Files\SplunkUniversalForwarder\etc\apps\TA-local-sysmon\local\inputs.conf**`
<img width="806" height="787" alt="Input_filter" src="https://github.com/user-attachments/assets/7355c6e6-94af-47a7-89c1-4a1ff3c79576" />

Now restart the forwarder:
<img width="761" height="454" alt="Restart_forwarder" src="https://github.com/user-attachments/assets/6ee105a7-b17e-4ed8-8136-ab43d8ebf9f6" />

Checking in kali machine for logs on newly created index = win

<img width="955" height="216" alt="index_win" src="https://github.com/user-attachments/assets/452efcf7-732f-4724-a93a-2e69ba024b88" />

<img width="955" height="978" alt="splunk_home_data" src="https://github.com/user-attachments/assets/7dc6d6aa-3322-41be-ad94-11da21eeaffc" />

Enable useful Windows logging with powershell:

```
wevtutil sl "Microsoft-Windows-PowerShell/Operational" /e:true
wevtutil sl "Microsoft-Windows-TaskScheduler/Operational" /e:true
wevtutil sl "Microsoft-Windows-Windows Defender/Operational" /e:true
```


Install Splunk Add-on for Sysmon which will help use to check the logs properly. 
<img width="934" height="479" alt="Sysmon_addon" src="https://github.com/user-attachments/assets/4be5e802-e557-4a5d-ab1d-ebfc500ad8d1" />


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
<img width="754" height="171" alt="Invoke_error" src="https://github.com/user-attachments/assets/bd6b6432-e8c4-4f27-8fed-5b5a394276c7" />
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

<img width="757" height="209" alt="invoke_fix" src="https://github.com/user-attachments/assets/e2752022-b071-4b8c-89c8-16c4178421a0" />

# T1053.005 (Persistence) | Scheduled Task: Create a task that runs a hidden script (every minute.)

Check of for modules of **T1053.005**
<img width="756" height="276" alt="T1053 005" src="https://github.com/user-attachments/assets/ea9412f1-de1c-488f-8f39-b92377a206e5" />
 The built-in test 8 -  T1053.005-8 Import XML Schedule Task with Hidden Attribute does **hidden** has similarity to the task.
 Execute and check if its ok
<img width="969" height="554" alt="T1053 005-8_done" src="https://github.com/user-attachments/assets/4177ef57-24bf-4c30-b1ea-8415686399f7" />

# T1218.005 (Defense Evasion) | MSHTA: Execute a malicious remote .hta file to bypass app control.

Check of modules of T1218.005
<img width="967" height="244" alt="T1218 005" src="https://github.com/user-attachments/assets/1497e215-7edc-4d2a-acd5-036f1cd896b5" />
As 3 is matches our requirements, we will use **T1218.005-3**
<img width="950" height="460" alt="T1218 005-3-done" src="https://github.com/user-attachments/assets/1eee7c2b-297c-40ec-9a37-fc8835d7d3d1" />

# T1003.001 (Credential Access) | LSASS Dumping: Use procdump to steal credentials from memory.

Check the module of **T1003.001**
<img width="975" height="260" alt="T1003 001" src="https://github.com/user-attachments/assets/f0d51ed5-ba0a-47dd-81ee-bf34ddfba5ea" />
As 1 matches our requirement, we will use **T1003.001-1**
For first run, windows defender blocked the process. So we had to disable the defender real-time protection then it worked and file has been written on **C:\Windows\Temp\lsass_dump.dmp**
<img width="796" height="587" alt="T1003 001-1-done" src="https://github.com/user-attachments/assets/345e233e-7913-4036-936a-4aefa8d573fc" />

# T1059.001 (Execution) | PowerShell Download:  Download and execute a script from the web.

Check the module of **T1059.001**
<img width="958" height="428" alt="T1059 001" src="https://github.com/user-attachments/assets/dcb72c26-c22c-4829-b4b1-f7db14a90d46" />
As option 6 and 8 both are good choice but 6 is purely on powershell. So we are using option 6.
<img width="741" height="162" alt="T1059 001-done" src="https://github.com/user-attachments/assets/1e7979e3-b065-47e2-8eac-bcdae6bcc6b9" />

# T1112 (Defense Evasion) | Registry Modification: Disable Windows Defender via

Check the modules of T1112 
<img width="811" height="908" alt="T1112" src="https://github.com/user-attachments/assets/3d1f171b-870c-4d1e-91ce-680938b63e28" />
For this task, we will use three sperate module. **T1112-38 (Suppress Win Defender Notifications),  T1112-51 (Disable Win Defender Notification) and T1112-56 (Tamper Win Defender Protection)**
<img width="829" height="423" alt="T1112-done" src="https://github.com/user-attachments/assets/0d9513e8-5cf5-4377-a73b-c31ec7b89519" />
**T1112-38 and T1112-51 completed successfully but T1112-56 was blocked by windows security but no notification has been popped because of other two options.**

# LOG Analysis

As we have installed **Splunk Add-on for Sysmon**, we need to do some changes to work with that addon.
App -> Manage App -> **Splunk Add-on for Sysmon**  -> Edit -> Visible (Yes)
<img width="1351" height="656" alt="sysmon_addon1" src="https://github.com/user-attachments/assets/9480f9b3-e3d7-4012-947d-755f51be37f9" />
<img width="852" height="553" alt="sysmon_addon-2" src="https://github.com/user-attachments/assets/04a8a538-a5b2-41ec-882f-3e6ca02e7051" />
Now a new menu will show in apps section
<img width="942" height="585" alt="sysmon_addon3" src="https://github.com/user-attachments/assets/db0d45f4-592e-443d-a47e-5b14848edd61" />

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
<img width="1919" height="757" alt="blue_T1053 005" src="https://github.com/user-attachments/assets/3a64b07c-5931-4ef4-9c4a-c5254b82382e" />


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
<img width="1755" height="755" alt="blue_T1218 005" src="https://github.com/user-attachments/assets/ded8c642-51f8-4d9b-9831-2007c62a54f3" />


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
<img width="1917" height="770" alt="blue_T1003 001" src="https://github.com/user-attachments/assets/0121447b-590b-46e8-8f11-310a74822216" />


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
<img width="1914" height="817" alt="blue_T1059 001" src="https://github.com/user-attachments/assets/cdc3b493-6198-4b31-87b0-2e436090389f" />


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
<img width="1881" height="819" alt="blue_T1112" src="https://github.com/user-attachments/assets/ecd8782d-4dfa-459f-bb95-542cd46eb3b1" />
