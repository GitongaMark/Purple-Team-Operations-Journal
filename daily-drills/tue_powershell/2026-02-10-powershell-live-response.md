# 🟦 Daily Drill: PowerShell for Live Response
**Date:** 2026-02-10

**Topic:** System Enumeration & Forensic Artifacts

**Focus:** "Living off the Land" (Using native tools to find threats)

## 1. Executive Summary
Today's drill focused on **System Enumeration**. I learned the cmdlets necessary to profile a Windows host, identify running code, map network connections, and audit user access—all without installing third-party tools.

## 2. Process & Persistence (The "What is running?" Check)
These commands are the first line of defense against malware.

| Cmdlet | Purpose / Forensic Value |
| :--- | :--- |
| **`Get-Process`** | Lists active processes. Look for high CPU/Memory or weird names. |
| **`Get-Service`** | Checks for stopped/running services. Malware often creates services for persistence. |
| **`Get-ScheduledTask`** | **Critical.** Malware loves hiding here to run on reboot. |
| **`Get-WinEvent`** | The modern replacement for `Get-EventLog`. fast querying of Security/System logs. |

## 3. Network Forensics (The "Who are you talking to?" Check)
| Cmdlet | Purpose / Forensic Value |
| :--- | :--- |
| **`Get-NetTCPConnection`** | The PowerShell `netstat`. Shows established connections (C2 beacons). |
| **`Get-DnsClientCache`** | Shows recent domains the machine resolved. Good for finding phishing domains. |
| **`Get-SmbShare`** | Audits file shares (like the `WorkShares` we exploited in "Dancing"). |
| **`Get-NetAdapter` / `Get-NetIPAddress`** | Basic IP and interface configuration. |

## 4. System Internals (The "Deep Dive")
| Cmdlet | Purpose / Forensic Value |
| :--- | :--- |
| **`Get-CimInstance`** | The modern version of `Get-WmiObject`. Queries hardware/OS info. |
| **`Get-HotFix`** | Lists installed patches. Helps identify vulnerabilities (e.g., "Is this machine patched against EternalBlue?"). |
| **`Get-MpPreference`** | Checks Windows Defender settings (e.g., verify Real-Time Protection is ON). |
| **`Get-PnpDevice`** | Lists hardware (e.g., identifying a malicious USB Key injection). |

## 5. File System & Integrity
| Cmdlet | Purpose / Forensic Value |
| :--- | :--- |
| **`Get-Item` / `Get-ItemProperty`** | Checks file metadata (Creation time, Size). |
| **`Get-Acl`** | Audits permissions. "Who can read this secret file?" |
| **`Get-FileHash`** | **Vital.** Generates MD5/SHA256 hashes to compare against VirusTotal. |
| **`Select-String`** | The PowerShell `grep`. Searches text files for patterns (e.g., "password"). |

## 6. User Auditing
| Cmdlet | Purpose / Forensic Value |
| :--- | :--- |
| **`Get-LocalUser`** | Lists users. Look for "admin" accounts created recently. |
| **`Get-LocalGroupMember`** | Checks who is in the "Administrators" group. |

## 🧠 Key Takeaway
`Get-WmiObject` is deprecated; **`Get-CimInstance`** is the future. Combining `Get-NetTCPConnection` (to find a suspicious IP) with `Get-Process` (to find the Process ID owning that connection) is the core workflow of a Blue Team investigation.