---
layout: post
title:  "Know Thy User Behavior"
date:   2025-11-19 19:30:00 -0700
categories: Log analysis
---

## User & Entity Behavior Analytics

Looking at standard Windows event logs alone rarely gives you a complete picture of what’s happening on a system. You may see a login, a process start, or a share access, but you won’t see the full story behind the user’s behavior.

This is where User and Entity Behavior Analytics (UEBA) fills in the gaps. UEBA takes multiple log sources, correlates them, and reveals how events relate to each other across systems, devices, users, and services. This type of information gathering can be mapped to MITREs ATT&CK Matrix to give visibility on a wide range of techniques. 

**Why Single Log Sources Aren’t Enough**

Scenario: an employee logging into their laptop at work:
Where do those logs appear?

- Domain Controller – user authentication
- Endpoint – local login events
- Switch – CAM table entries
- DHCP – address assignment
- DNS – name resolution as the device reaches out
- Connection Metadata – Zeeks conn.log
- Proxy logs
- Remote terminal logs
- Application logs
- Web logs

A single action creates dozens of events across multiple systems.

Relying on one source, like Windows Event Logs, limits the context of the story. UEBA restores that context by correlating everything. Multiple logs, multiple data sources. 

**What UEBA Does: Events + Context = Story**

UEBA doesn’t just look at events. It looks at:
- What happened
- Who did it
- Where it occurred
- How unusual it is compared to typical behavior

This lets you see patterns traditional log review would miss.

A great example is LogonTracer by JPCERT. It can visualize Active Directory authentication paths, showing suspicious logons or lateral movement attempts in a graph, allowing you to investigate visually.

**Detection Tuning**

John Strand, from Black Hills Information Security, argues that “false positives” don’t exist. Instead, there are detection tuning opportunities. His premise to detection tuning is if an alert fires, something triggered it. 

It could be:
- A legitimate service doing something odd
- A misconfigured system
- A benign but unusual user action

You tune your detection rules to your environment until they accurately reflect your normal. 

**How UEBA Works**

Think of a point system.

Every user logon as a +1
Every user logoff as a -1

A normal user in a normal day might:
- Log in
- Access a few shares
- Authenticate to one or two servers

Maybe +3 to +5 “behavior points.”

A malicious actor performing lateral movement might generate:
- 50 logon attempts
- 30 share accesses
- 20 authentication failures

That’s +100 or more, which you can clearly see is abnormal. 
UEBA builds baselines of typical activity. Then it identifies behavior that deviates from that baseline.

**Adding AI to UEBA**

AI-driven UEBA can learn:
- How often a user logs in
- How many file shares they typically access
- Which systems they interact with
- How often they authenticate
- Typical volumes of activity

If Paul accesses 10–15 file shares a day, that becomes baseline.
If Paul suddenly accesses every file on the server, that’s an anomaly.

This is the core of UEBA - flagging behavior that does not match historical norms.

**Using Adversary Emulation to Improve UEBA**

Once you know all your log sources, the next step is adversary emulation:
1. Run an attack simulation
2. Observe which logs are generated
3. Build or tune signatures based on the attack
4. Repeat until visibility is complete

This ensures your UEBA detections match the actual behavior of attackers, not just theoretical patterns.

Red Canary's Atomic Red Team(https://redcanary.com/atomic-red-team/) can be a great tool for this.

**Active Directory Logs**

AD logs are the single most useful source of information. 

AD logs reveal:
4624: Successful Logon
4625: Logon Failure
4768: Kerberos Authentication (TGT Request)
4769: Kerberos Service Ticket (ST Request)
4776: NTLM Authentication
4672: Assign Special Privileges

They are noisy but extremely powerful. When correlated with UEBA and proactive tuning, they expose lateral movement, brute forcing, privilege abuse, and rogue authentication.

Tools That Support UEBA Concepts
- DeepBlueCLI
Analyzes Windows logs for patterns indicating malicious behavior.

- DeepWhiteCLI
Extends analysis with Sysmon logs and uses VirusTotal lookups for known malware.

These aren’t full UEBA platforms, but they provide pattern-based detection that mirrors UEBA principles.

**Volume vs. Value**

Ask yourself - in your own environment:
- How much do you log?
- What do you log from?
- Who decides what you log?
- What percentage of logs have signatures or alerts?

For most organizations:
Only 5–10% of logs have alerting rules.
Meaning 90–95% of logs never generate alerts.

Many vendors encourage “log everything,” which increases storage costs and justifies selling more data lakes. Be wary of these kind of vendor tactics and techniques. 

Mini-Lab: See the Problem Yourself
1. On your windows machine press (win + R), type (eventvwr.msc)
2. Under 'Windows Logs' go to 'Application' 
3. Take a look at all the logs that the system generated. Clicking on one will give you information below in 'General'
4. Next go to 'Security'
5. Here, will be a ton of the same Event IDs that don't give much context

This is why Windows event logs are insufficient without correlation.

**Command-Line Logging, PowerShell Logging, and Sysmon**

Windows event logs are limited. To get meaningful detail, enable:
- Command-line logging
Shows exact commands a user or process executed. 

- PowerShell logging
Can expose scripts, and encoded commands. 

Sysmon gives you the security, configuration, and analysis for a system to be successful. 
The details you get:
- UTC timestamps
- Process IDs
- Image names
- File versions
- Original file names
- Company/manufacturer
- Usernames
- Hashes (MD5, SHA256, IMPHASH)
- Parent processes
- And far more.

With Group Policy, you can place Sysmon and its XML configuration on a file share, and all systems assigned to that Group Policy object will automatically inherit and apply those configurations.

A great tool to write signatures for Sysmon logs - Sigma. With Sigma you can write a rule in 'Sigma" language and convert it into a wide variety of different SIEM languages, like Splunk or Elastic. 

**Conclusion**

UEBA transforms raw log data into behavioral context. Combining UEBA with AD logs, Sysmon, Sigma, and adversary emulation creates a detection capability far deeper than traditional SIEMs or event logs alone. When it comes to detection tuning this is a proactive approach that can help reduce false positives. 