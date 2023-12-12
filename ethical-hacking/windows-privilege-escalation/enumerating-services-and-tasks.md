# Enumerating Services & Tasks

## Here are some useful scripts to enumerate Services & Tasks.

## PowerShell - List running Services

```powershell
PS/> Get-Service
```

```powershell
PS/> Get-WmiObject -class Win32_Service -Property Name, DisplayName, PathName, StartMode | Where { $_.PathName -notlike "C:\Windows*" } 
| select Name,StartMode,PathName
```

## PowerShell - List Scheduled Tasks

```powershell
PS/> Get-ScheduledTask
```

```powershell
PS/> tasklist
```

## Emumerate Service DLL's.

We can view the DLL's a service loads calling it's process:

```powershell
Get-Process -Name RemoteServerWin | Select-Object -Expand Property Modules | Select-Object FileName

(Get-Process -Name "msedge").Modules
```

## Enumerating Permissions

In order for us to leverage autorun service and scheduled tasks to escalate privileges, we need to have write privileges on a process that is run as a escalated user.&#x20;

First, we will see how we can use the icacls command to check the permissions of folder and file ACLs.

The permissions we are looking for on the folder are any one of the following three permissions:

(F) Full Control

&#x20;(M) Modify

&#x20;(W) Write The user / group permissions we are looking for are the following:

The user we are currently logged in as (%USERNAME%) Authenticated Users Everyone BUILTIN\Users NT AUTHORITY\INTERACTIVE

#### <mark style="color:red;">NOTE:</mark> Use BOTH of these tools!

### icacls

```powershell
PS/> icacls "C:\Program Files (x86)\Unified Remote 3"
```

### accesschk64 - SysInternalsSuite

```powershell
.\accesschk64.exe -wvud "C:\Users\Tom\AppData\Local\Microsoft\OneDrive" -accepteula

.\accesschk64.exe -wvud "C:\Program Files (x86)\Unified Remote 3" -accepteula
```
