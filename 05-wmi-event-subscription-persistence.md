# WMI Event Subscription Persistence

## Description

Windows Management Instrumentation (WMI) Event Subscriptions provide a powerful mechanism for automatically executing actions when specific system events occur.

WMI was designed for system administration and automation, but attackers frequently abuse permanent WMI event subscriptions to maintain persistence across reboots and user logons.

Unlike registry keys, startup folders, or scheduled tasks, WMI persistence is stored within the WMI repository, making it less visible during routine investigations.

---

## WMI Persistence Components

Permanent WMI subscriptions consist of three components:

### `Event Filter`

An Event Filter defines the event that WMI monitors.

Common examples include:

* System startup
* User logon
* Process creation
* Time-based events

---

### `Event Consumer`

An Event Consumer defines the action to perform when the filter condition is met.

Common actions include:

* Executing a command
* Running a script
* Launching an application

---

### `Filter-To-Consumer Binding`

The binding connects the Event Filter to the Event Consumer.

When the configured event occurs, the associated action is automatically executed.

---

## WMI Repository Location

Permanent WMI subscriptions are typically stored under:

```bash
root\subscription
```

Common WMI classes involved:

```bash
__EventFilter

CommandLineEventConsumer

ActiveScriptEventConsumer

__FilterToConsumerBinding
```

---

## Why Attackers Use WMI Persistence

WMI persistence is popular because it:

* Survives system reboots
* Executes automatically
* Does not require startup folders
* Does not require registry Run keys
* Can be overlooked during basic investigations

---

## WMI Event Subscription Enumeration

### Enumerate Event Filters

```powershell
Get-WmiObject -Namespace root\subscription -Class __EventFilter
```

---

### Enumerate Event Consumers

```powershell
Get-WmiObject -Namespace root\subscription -Class CommandLineEventConsumer
```

---

### Enumerate Filter Bindings

```powershell
Get-WmiObject -Namespace root\subscription -Class __FilterToConsumerBinding
```

---

### Enumerate All Subscription Objects

```powershell
Get-WmiObject -Namespace root\subscription
```

---

## Investigation Checklist

Review the following:

* Event filter names
* WQL queries
* CommandLineEventConsumer commands
* Script consumers
* Referenced executables
* Execution paths
* Recently created subscription objects

Pay special attention to:

* PowerShell execution
* Script-based consumers
* Executables located in user-writable directories
* Obfuscated commands

---

## Common Suspicious Indicators

* Unknown WMI subscriptions
* Encoded PowerShell commands
* Commands executing from Temp directories
* Event consumers launching scripts
* WMI objects created near the compromise timeline

---

## WMI Investigation Workflow

1. Enumerate Event Filters
2. Enumerate Event Consumers
3. Review Filter-to-Consumer Bindings
4. Identify executed commands
5. Validate executable paths
6. Correlate creation times with other persistence artifacts
7. Determine whether the subscription is legitimate or malicious

---

## Summary

WMI Event Subscription persistence is an advanced persistence mechanism that allows code execution based on system events. Because the configuration is stored within the WMI repository rather than traditional startup locations, it can remain hidden unless investigators specifically examine WMI artifacts.
