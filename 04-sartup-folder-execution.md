# Startup Folder Execution (Windows Persistence)

## Description

Windows Startup folders are special directories that automatically execute programs when a user logs in. Attackers frequently abuse these folders to maintain persistence by placing malicious executables, scripts, or shortcuts that launch during user logon.

---

## Startup Folder Locations

### Current User Startup Folder

```bash
C:\Users\<USER>\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup
```

---

### All Users Startup Folder

```bash
C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup
```

Programs placed inside these folders automatically execute when users log in.

---

## Suspicious Indicators in Startup Folder Analysis

Investigate startup folder entries that contain :

- PowerShell, CMD, or VBScript launchers
- Obfuscated shortcut targets
- Random or fake system names
- Hidden files
- Recently created startup entries
- Scripts (``.ps1``, ``.vbs``, ``.js``, ``.bat``)
- Unknown ``.lnk`` shortcut files

Example Malicious Startup Entry : 

```powershell
Mode   LastWriteTime         Length Name
----   -------------         ------ ----
-a---- 5/15/2026 10:42 AM      2048 WindowsUpdate.lnk
```

This is suspicious because :

- File executes from a public directory
- Startup shortcut mimics legitimate Windows update behavior
- Payload automatically executes at user logon