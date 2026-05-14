# Registry-based persistence

Registry-based persistence is one of the most common persistence mechanisms used by attackers to automatically execute malicious payloads during system startup or user logon.

Windows provides multiple registry locations that allow programs to launch automatically. Adversaries abuse these locations to maintain persistence without requiring user interaction.

---

## Persistence Overview

Registry-based persistence investigations focus on Windows autostart mechanisms that allow malicious payloads to execute automatically during system startup or user logon. 

Common techniques include abuse of **Run and RunOnce registry keys**, **Winlogon modifications**, **IFEO hijacking**, **Explorer shell extensions**, and **Userinit or Shell manipulation**. 

These mechanisms can provide user-level or system-wide persistence while enabling stealth execution and payload chaining during attacker operations.

---

## Important Registry Locations

### Run Keys

Run keys are Windows registry locations used to automatically execute programs during user logon or system startup. Attackers commonly abuse these keys to maintain persistent access by launching malicious payloads every time the user signs in or the system boots.

```bash
## Current User
HKCU\Software\Microsoft\Windows\CurrentVersion\Run

## Local Machine
HKLM\Software\Microsoft\Windows\CurrentVersion\Run
```

---

### RunOnce Keys

RunOnce keys are Windows registry locations used to execute programs only one time during the next user logon or system startup. After execution, the registry value is automatically removed by Windows. Attackers may abuse these keys for temporary persistence, payload staging, malware installation, or cleanup operations.

```bash
## Current User
HKCU\Software\Microsoft\Windows\CurrentVersion\RunOnce

## Local Machine
HKLM\Software\Microsoft\Windows\CurrentVersion\RunOnce
```

> The main difference between Run and RunOnce keys is the execution frequency. Run keys execute programs every time the user logs in, while RunOnce keys execute programs only one time before the registry entry is automatically removed.

---

### Winlogon Keys

Winlogon registry keys control important user logon processes in Windows, including the default shell and user initialization programs. Attackers abuse these keys to launch malicious executables automatically during user logon before the desktop fully loads.

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

### IFEO (Image File Execution Options)

Image File Execution Options (IFEO) is a Windows registry feature originally designed for debugging applications. Attackers abuse IFEO by adding a malicious debugger value to legitimate executables, causing Windows to launch malware whenever the targeted application starts.

```bash
HKLM\Software\Microsoft\Windows NT\CurrentVersion\Image File Execution Options
```

Common attacker techniques:
- Hijacking `utilman.exe`, `sethc.exe`, or `cmd.exe`
- Redirecting legitimate programs to malware loaders
- Launching payloads through fake debugger executables
