# Scheduled Tasks Abuse

## Description

Scheduled Tasks are a built-in Windows mechanism used to automate execution of programs based on time, system events, or user actions. Attackers frequently abuse them for persistence because they can execute payloads stealthily under user or SYSTEM context and survive reboots.

---

## Storage Locations

Scheduled tasks are stored in multiple locations:

```bash
C:\Windows\System32\Tasks\
```

Registry (task metadata tracking):

```bash
HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Schedule
```

---

## Enumeration Methods

### Basic PowerShell Enumeration

```bash
Get-ScheduledTask
```

---

### Scheduled Tasks Enumeration (Non-Microsoft + JSON)

```powershell
Get-ScheduledTask |
Where-Object {$_.TaskPath -notlike "\Microsoft*"} |
ForEach-Object {
    $task = $_
    $info = Get-ScheduledTaskInfo -TaskName $task.TaskName -TaskPath $task.TaskPath

    [PSCustomObject]@{
        TaskName        = $task.TaskName
        TaskPath        = $task.TaskPath
        State           = $info.State
        LastRunTime     = $info.LastRunTime
        LastResult      = $info.LastTaskResult
        NextRunTime     = $info.NextRunTime
        Actions         = ($task.Actions | ForEach-Object { $_.Execute + " " + $_.Arguments })
        Triggers        = ($task.Triggers | ForEach-Object { $_.TriggerType })
        PrincipalUser   = $task.Principal.UserId
    }
} | ConvertTo-Json -Depth 5
```

---

### CMD Enumeration

```bash
schtasks /query /fo LIST /v

schtasks /query /fo TABLE /v
```

---

### File System Enumeration

```bash
dir C:\Windows\System32\Tasks /s
```

---

### Registry Enumeration

```powershell
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Schedule" /s
```

---

### Event Log Investigation

- Event ID : **4698 → Task Created**
- Event ID : **4702 → Task Updated**
- Event ID : **4699 → Task Deleted**
- Event ID : **4700 / 4701 → Task Enabled/Disabled**
- Event ID : **4688 → Process Execution Correlation**

---

## Suspicious Indicators in Scheduled Task Analysis

During DFIR investigations, scheduled tasks should be carefully analyzed for signs of malicious persistence. The following indicators are commonly associated with attacker activity:

- Tasks located outside `\Microsoft\` directories (custom or unknown TaskPath)
- Execution of scripting engines such as:
  - `powershell.exe`
  - `cmd.exe`
  - `wscript.exe`
  - `mshta.exe`
- Encoded or obfuscated command-line arguments (e.g., PowerShell `-enc`)
- Tasks running from suspicious directories:
  - `%AppData%`
  - `%Temp%`
  - `C:\Users\Public\`
  - `Downloads`
- Random or system-like task names (e.g., `WindowsUpdateService123`)
- SYSTEM-level execution with unknown or unsigned binaries
- High-frequency triggers (every few minutes → beacon-like behavior)
- Tasks with unusual or missing descriptions
- Mismatch between task name and executed binary (masquerading)
