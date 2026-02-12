# 📜 Daily Drill: Sigma Rules (Defense Evasion)

**Date:** 2026-02-12  
**Topic:** Sigma Rule Conversion 
**Focus:** Windows Defender Tampering  

---

## 1. The Threat Scenario

Attackers often disable security tools immediately after gaining access to avoid detection. This is classified as **Defense Evasion (T1562.001)** in MITRE ATT&CK. Detecting the moment Windows Defender is turned off is a high-fidelity signal of compromise.

---

## 2. The Sigma Rule (Source Code)

**Title:** Windows Defender Threat Detection Disabled  
**Description:** Detects events where Windows Defender real-time protection or scanning is disabled.

```yaml
title: Windows Defender Threat Detection Disabled
id: (UUID-Placeholder)
status: experimental
description: Detects when Windows Defender features are disabled via registry or group policy.
logsource:
    product: windows
    service: windefend
detection:
    selection:
        EventID:
            - 5001
            - 5010
            - 5012
            - 5101
    condition: selection
level: high
```
---

## 3. The Conversion Process

I used the `sigma-cli` tool to compile the YAML rule into Splunk-compatible SPL.

**Command:**

```bash
sigma convert \
    --target splunk \
    --pipeline splunk_windows \
    ./rules/windows_defender_disabled.yml
```
---

## 4. The Result (Splunk Query)

The compiler correctly mapped the generic `service: windefend` to the specific Windows Event Log source used by Splunk.

```spl
source="WinEventLog:Microsoft-Windows-Windows Defender/Operational"
EventCode IN (5001, 5010, 5012, 5101)
```
---

## 5. Analysis: What Do These Codes Mean?

To make this rule actionable, we must understand the specific trigger for each Event ID:

- **5001** — Real-time protection was disabled *(the smoking gun)*  
- **5010** — Scanning for malware/spyware is disabled  
- **5012** — Virus scanning is disabled  
- **5101** — Antimalware platform architecture is suspicious *(often indicates tampering)*  

---

## 🧠 Key Takeaway

By grouping these four Event IDs into a single `IN` statement, we create a robust **Tamper Alert** that detects attacker attempts to disable Windows Defender—regardless of which specific component they target.

---

## 🎯 MITRE ATT&CK Mapping

**Tactic:** Defense Evasion  
**Technique:** Impair Defenses  
**Sub-technique:** T1562.001 — Disable or Modify Tools  

### Why This Matters
Adversaries commonly disable Windows Defender immediately after gaining initial access or privilege escalation. These events often occur **early in the intrusion lifecycle**, making them high-value signals for rapid detection and response.

**Mapped Signals:**
- Disabling real-time protection
- Disabling malware/virus scanning
- Tampering with the antimalware platform itself

This detection directly supports identifying **post-compromise defense evasion activity**.

---