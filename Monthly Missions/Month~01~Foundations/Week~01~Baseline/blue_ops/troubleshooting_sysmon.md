# 🔧 Troubleshooting Log: The Case of the Silent Sysmon

> A real-world SIEM troubleshooting case study involving Splunk Universal Forwarder, Sysmon, and Windows Event Log permissions.

---

## 📌 Overview

**Date:** February 3, 2026  
**Environment:**  
- Splunk Enterprise (Ubuntu)  
- Splunk Universal Forwarder (Windows 10 Victim VM)  
- Sysmon enabled (Operational channel)

**Incident:**  
Splunk Universal Forwarder successfully connected to the Splunk indexer but failed to ingest **Sysmon Event ID 3 (Network Connections)**.

**Status:** ✅ Resolved

---

## 🧠 Problem Statement

The Windows 10 victim machine was forwarding:
- Security logs
- System logs
- Application logs

However, **Sysmon telemetry was completely absent**, despite Sysmon being installed and generating events locally.

---

## 🔍 1. Symptoms Observed

**Splunk Search Used:**

```splunk
index=* source="*Sysmon*"

Result:
❌ 0 events found

This confirmed that Sysmon logs were not being indexed at all.

🕵️ 2. Root Cause Analysis
Phase 1: Network & Firewall Validation (Layer 3/4)

Hypothesis: Network or firewall blockage.

Tests Performed:

Ping Test

Windows → Ubuntu

✅ Success

Port Connectivity Check

Test-NetConnection -ComputerName 192.168.122.1 -Port 9997


✅ TcpTestSucceeded : True

Socket Verification

netstat -an | findstr 9997


✅ ESTABLISHED

Conclusion:
Network path is healthy. Issue exists at the application layer.

Phase 2: Forwarder Configuration Audit

Hypothesis: Sysmon logs are not configured for ingestion.

Inspected File:

C:\Program Files\SplunkUniversalForwarder\etc\system\local\inputs.conf


Finding:
Default Windows Event Log stanzas existed, but Sysmon was missing.

Fix Attempt:

[WinEventLog://Microsoft-Windows-Sysmon/Operational]
disabled = 0
renderXml = 1


Result:
❌ Still no Sysmon data.

Phase 3: Time Skew Investigation

Hypothesis: Events are indexed with incorrect timestamps.

Findings:

VM Time: 02:22 AM (Pacific Time)

Actual Time: 13:40 PM (UTC+3, Nairobi)

Impact:
Splunk time-based searches excluded events as historic.

Fix:

Synced VM time to UTC+3

Searched using All Time

Result:
❌ Still no Sysmon events.

Phase 4: Internal Log Forensics (Breakthrough)

Hypothesis: Forwarder is failing silently.

Log Analyzed:

Select-String "Sysmon" "C:\Program Files\SplunkUniversalForwarder\var\log\splunk\splunkd.log"


Critical Finding:

ERROR ExecProcessor ... WinEventLogChannel::init: Init failed ... errorCode=5


Analysis:

Windows Error Code 5 = Access Denied

SplunkForwarder service account lacked permission to read:

Microsoft-Windows-Sysmon/Operational

🛠️ 3. Resolution
Step 1: Privilege Escalation (Legitimate)

Opened services.msc

Located SplunkForwarder

Changed Log On As:

❌ Virtual Account

✅ Local System Account

Step 2: Restart & Validation

Restarted SplunkForwarder service

Verified splunkd.log

✅ Error Code 5 disappeared

Generated Sysmon activity:

nmap -A <target>

Step 3: Final Verification

Splunk Search:

index=* source="*Sysmon*" EventCode=3


Result:
✅ Sysmon network telemetry successfully ingested.

🎯 Key Takeaways

“Connected does not mean working.”

A valid TCP connection only proves transport

Permissions decide ingestion

When data is missing but the network is healthy:

Always check internal application logs

Especially: splunkd.log

🧩 Skills Demonstrated

SIEM troubleshooting

Windows Event Log internals

Splunk Universal Forwarder debugging

Sysmon telemetry validation

Time-skew analysis

Log-based root cause analysis

📂 Use Case

This case study is relevant for:

SOC Analysts

Blue Teamers

Purple Teamers

Detection Engineers

DFIR Practitioners

SIEM Engineers