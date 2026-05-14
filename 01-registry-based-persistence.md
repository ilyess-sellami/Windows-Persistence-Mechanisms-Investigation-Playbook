# Registry-based persistence

## Description

Registry-based persistence is one of the most common persistence mechanisms used by attackers to automatically execute malicious payloads during system startup or user logon.

Windows provides multiple registry locations that allow programs to launch automatically. Adversaries abuse these locations to maintain persistence without requiring user interaction.

---

## Persistence Overview

Registry-based persistence investigations focus on Windows autostart mechanisms that allow malicious payloads to execute automatically during system startup or user logon. 

Common techniques include abuse of **Run and RunOnce registry keys**, **Winlogon modifications**, **IFEO hijacking**, **Explorer shell extensions**, and **Userinit or Shell manipulation**. 

These mechanisms can provide user-level or system-wide persistence while enabling stealth execution and payload chaining during attacker operations.

---

## Important Registry Locations

### ``Run`` Keys

``Run`` keys are Windows registry locations used to automatically execute programs during user logon or system startup. Attackers commonly abuse these keys to maintain persistent access by launching malicious payloads every time the user signs in or the system boots.

```bash
HKCU\Software\Microsoft\Windows\CurrentVersion\Run

HKLM\Software\Microsoft\Windows\CurrentVersion\Run
```

---

### ``RunOnce`` Keys

``RunOnce`` keys are Windows registry locations used to execute programs only one time during the next user logon or system startup. After execution, the registry value is automatically removed by Windows. Attackers may abuse these keys for temporary persistence, payload staging, malware installation, or cleanup operations.

```bash
HKCU\Software\Microsoft\Windows\CurrentVersion\RunOnce

HKLM\Software\Microsoft\Windows\CurrentVersion\RunOnce
```

> The main difference between Run and RunOnce keys is the execution frequency. Run keys execute programs every time the user logs in, while RunOnce keys execute programs only one time before the registry entry is automatically removed.

---

### ``RunServices`` & ``RunServicesOnce`` Keys

``RunServices`` registry keys are legacy Windows startup locations used to automatically launch background services during system startup. Attackers may abuse these keys to execute malicious programs before user logon and maintain persistence across reboots.

`RunServicesOnce` works similarly but executes the program only one time before the registry entry is removed.

```bash
HKLM\Software\Microsoft\Windows\CurrentVersion\RunServices

HKLM\Software\Microsoft\Windows\CurrentVersion\RunServicesOnce
```

---

### ``Winlogon`` Keys

``Winlogon`` registry keys control important user logon processes in Windows, including the default shell and user initialization programs. Attackers abuse these keys to launch malicious executables automatically during user logon before the desktop fully loads.

```bash
HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon
```

Commonly targeted values:
- `Shell`
- `Userinit`

Malware persistence often works by:
- Replacing `explorer.exe` with a malicious executable
- Adding malware alongside legitimate `userinit.exe`
- Launching hidden payloads during the logon process

---

### ``IFEO (Image File Execution Options)``

``Image File Execution Options (IFEO)`` is a Windows registry feature originally designed for debugging applications. Attackers abuse IFEO by adding a malicious debugger value to legitimate executables, causing Windows to launch malware whenever the targeted application starts.

```bash
HKLM\Software\Microsoft\Windows NT\CurrentVersion\Image File Execution Options
```

Common attacker techniques:
- Hijacking `utilman.exe`, `sethc.exe`, or `cmd.exe`
- Redirecting legitimate programs to malware loaders
- Launching payloads through fake debugger executables

---

### ``StartupApproved`` Keys

``StartupApproved`` registry keys are used by Windows to track whether startup programs are enabled or disabled. Attackers may manipulate these keys to re-enable malicious startup entries or hide persistence mechanisms from normal startup management tools.

```bash
HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\StartupApproved

HKLM\Software\Microsoft\Windows\CurrentVersion\Explorer\StartupApproved
```

---

### ``Policies Explorer Run``

``Policies\Explorer\Run`` registry keys are Windows policy-based startup locations used to automatically execute programs during user logon. Attackers may abuse these keys to establish persistent execution through Windows policy mechanisms.

```bash
HKCU\Software\Microsoft\Windows\CurrentVersion\Policies\Explorer\Run

HKLM\Software\Microsoft\Windows\CurrentVersion\Policies\Explorer\Run
```

---

### ``Windows`` Registry Key (AppInit / Load Behavior)

``Windows`` controls system-wide Windows behavior related to application initialization and DLL loading. Attackers may abuse this key—especially `AppInit_DLLs`—to force malicious DLLs to load into multiple processes for persistent execution.

```bash
HKLM\Software\Microsoft\Windows NT\CurrentVersion\Windows
```

---

### ``Explorer`` Hooks

``Explorer`` hook persistence refers to registry modifications that influence or extend Windows Explorer behavior. Attackers may abuse these locations to automatically execute malicious code when Explorer starts or when a user logs in.

```bash
HKLM\Software\Microsoft\Windows\CurrentVersion\Explorer

HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer
```

---

## Registry Key Enumeration

Registry persistence keys can be investigated using PowerShell, Command Prompt, or the Windows Registry Editor (`regedit.exe`).

### Enumerate Using PowerShell

```powershell
reg query "HKCU\Software\Microsoft\Windows\CurrentVersion\Run"

reg query "HKLM\Software\Microsoft\Windows\CurrentVersion\Run"
```

---

### Enumerate Using Registry Editor

Open Registry Editor:

```bash
Win + R → regedit
```

Navigate to a registry path manually:

```bash
Computer\HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run
```

Review:

- Registry values
- Executable paths
- Suspicious startup entries
- Obfuscated commands
