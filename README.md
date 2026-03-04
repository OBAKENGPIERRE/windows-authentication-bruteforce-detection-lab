🔐 Windows Authentication Monitoring & Brute Force Detection Lab
📌 Overview

This project simulates both local and remote brute-force authentication attempts against a Windows system and demonstrates how these attacks appear in Windows Security logs.

The lab analyzes key security events including:

- Event ID 4625 – Failed Logon
- Event ID 4624 – Successful Logon
- Event ID 4740 – Account Lockout

Attack simulations were performed from a Kali Linux system using password-guessing techniques against SMB authentication.

The objective was to practice authentication monitoring, log correlation, and structured incident investigation in a controlled SOC lab environment.

🖥️ Lab Environment

VMware Workstation Pro

Windows 11 Pro (Target System)

Host-only isolated virtual network

Local test account: testuser

Administrative account used for log investigation

⚙️ Logging Configuration

The following audit policies were configured to increase visibility:

Audit Logon Events (Success & Failure)

Audit Account Logon Events

Process Creation Logging (Event ID 4688)

Command-line process auditing via Group Policy

PowerShell Script Block Logging

🚨 Attack Simulation

A simulated brute-force scenario was conducted by repeatedly entering incorrect credentials for the account testuser.

This generated multiple failed authentication events.

Event ID 4625 – Failed Logon

Observed:

Account Name: testuser

Logon Type: 2 (Interactive)

Failure Reason: Unknown username or bad password

Source Network Address: 127.0.0.1
![4625 Failed Logons](screenshots/4625-failed-logons.png)

After multiple failed attempts, a successful login was recorded.

Event ID 4624 – Successful Logon

Observed:

Account Name: testuser

Logon Type: 2 (Interactive)

Source Network Address: 127.0.0.1

Timestamp correlation confirmed successful authentication following repeated failures
![4624 Successful Logon](screenshots/4624-successful-logon.png)

🔎 Investigation Methodology
![Detailed Event View](screenshots/detailed-event-view.png)

The investigation process included:

Filtering Security logs by Event ID (4625, 4624)

Filtering by specific user account

Reviewing logon types

Analyzing source IP address

Performing timeline reconstruction

Determining likelihood of malicious activity

🧠 Key Findings

Multiple failed interactive logon attempts were detected.

The successful login originated from localhost (127.0.0.1).

Logon Type 2 confirmed direct keyboard interaction.

No evidence of remote network-based brute-force activity was identified.

Conclusion: Activity likely resulted from incorrect password entry rather than malicious authentication attack.

🛡️ Skills Practiced

Windows Security Event Log analysis

Authentication monitoring

Event correlation

Logon Type interpretation

Basic incident reporting

SOC-style investigation workflow

📚 MITRE ATT&CK Mapping

Technique: T1110 – Brute Force

Although conducted locally, the investigation methodology aligns with brute-force authentication detection practices.

🔎 Detection Logic (SOC Perspective)

If this activity were observed in a production environment, the following detection logic could be implemented:

Potential Brute Force Indicator:

Multiple Event ID 4625 entries

Same TargetUserName

Within short time window (e.g., 5–10 attempts in under 2 minutes)

Escalation Criteria:

4625 failures followed by 4624 success

Logon Type 3 (Network) or 10 (Remote Desktop)

External Source IP address

Example detection condition:

If more than 5 failed authentication attempts occur for the same user within 2 minutes, trigger security alert.
This logic aligns with authentication monitoring best practices in SOC environments.




Network Brute Force Simulation (Remote Authentication Attack)

A second test scenario simulated a remote authentication attack from a Kali Linux system against the Windows target machine.

The attack was executed using NetExec (nxc) to attempt multiple password guesses against the SMB service.

Attack Source

Kali Linux attacker machine
IP Address: 192.168.100.129

Target

Windows 11 system
IP Address: 192.168.100.128

Tool Used
nxc smb 192.168.100.128 -u testuser --password-file /usr/share/wordlists/rockyou.txt
Observed Security Events

Multiple authentication failures were recorded:
![Network Bruteforce](screenshots/4625-network-bruteforce.png)

Event ID 4625 – Failed Logon

Key indicators:

Logon Type: 3 (Network)

Account Name: testuser

Source Network Address: 192.168.100.129

![4625 Network Event Details](screenshots/4625-network-event-details.png)
This confirms the authentication attempts originated from a remote system.

Account Lockout

![4625 Account Lockout Event](screenshots/account-lockout-event.png)
Windows eventually locked the account after repeated failures.

Event ID 4740 – Account Locked Out

This behavior demonstrates how repeated brute-force attempts can trigger defensive account lockout policies.
