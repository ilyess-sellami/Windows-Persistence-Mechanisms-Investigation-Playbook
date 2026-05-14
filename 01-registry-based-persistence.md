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
