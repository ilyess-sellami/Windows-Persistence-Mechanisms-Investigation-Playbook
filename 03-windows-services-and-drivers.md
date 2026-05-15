# Windows Services & Drivers Persistence

## Description

Windows Services and Drivers are core operating system components used to run background processes and kernel-level functionality. Attackers frequently abuse them for persistence because they can execute automatically during system boot and often run with high privileges such as SYSTEM.

---

## Registry Locations

### Services Registry Key

```bash
HKLM\SYSTEM\CurrentControlSet\Services
```

This registry key stores:

- Service configurations
- Driver configurations
- Startup types
- Binary execution paths

---

### Service Executable Files

The actual binaries are usually stored in:

```bash
C:\Windows\System32\
```

Common examples:

- ``svchost.exe``
- ``services.exe``
- service DLLs

---

## Enumeration Methods

### PowerShell Enumeration

Enumerate All Services :

```powershell
Get-Service
```

Detailed Service Enumeration :

```powershell
Get-WmiObject Win32_Service |
Select-Object Name, DisplayName, State, StartMode, PathName
```

JSON Enumeration (DFIR-Friendly) :

```powershell
Get-WmiObject Win32_Service |
ForEach-Object {
    [PSCustomObject]@{
        Name        = $_.Name
        DisplayName = $_.DisplayName
        State       = $_.State
        StartMode   = $_.StartMode
        PathName    = $_.PathName
        StartName   = $_.StartName
    }
} | ConvertTo-Json -Depth 3
```

---

### CMD Enumeration

List Services :

```cmd
sc query type= service state= all
```

Query Specific Service :

```cmd
sc qc <service_name>
```

---

### Registry Enumeration

```powershell
reg query "HKLM\SYSTEM\CurrentControlSet\Services" /s
```

---

### Event Log Investigation

- Event ID : **7045/4697 → Service Installed**
- Event ID : **7036 → Service State Change**

---

## Suspicious Indicators in Service Analysis

Investigate services that show :

- Random or fake Windows-like names
- Services running from :
    - ``%Temp%``
    - ``%AppData%``
    - ``C:\Users\Public\``
- Unsigned executables
- PowerShell or CMD execution
- SYSTEM services with unknown binaries
- AutoStart services with suspicious paths
- Drivers loaded from non-standard directories
- Missing company/vendor information
- Services masquerading as legitimate Windows components

Example Malicious Service : 

```powershell
Name        : WindowsUpdateService
State       : Running
StartMode   : Auto
PathName    : C:\Users\Public\updater.exe
StartName   : LocalSystem
```

This service is suspicious because :

- Name mimics legitimate Windows behavior
- Executes from ``C:\Users\Public\``
- Runs automatically at boot
- Executes under SYSTEM privileges
