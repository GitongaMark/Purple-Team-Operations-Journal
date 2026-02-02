# 🧠 Daily Drill: KQL Basics & Regex Patterns
**Date:** 2026-02-02
**Focus:** Data Structures & Pattern Matching

## 1. KC7 Cyber (KQL Basics)
**Topic:** Tables, Columns, and Rows
Learned the schema of standard security datasets. These tables map directly to real-world logs I will see in Splunk/Sentinel.

| Table Name | Description / Use Case |
| :--- | :--- |
| **AuthenticationEvents** | Tracking logins (Success/Fail). Critical for detecting Brute Force or Impossible Travel. |
| **Email** | Phishing analysis (Sender, Subject, Links). |
| **Employees** | Context for users (VIPs, Dept). Helps spot anomalies (e.g., HR accessing Engineering DB). |
| **FileCreationEvents** | Malware dropping artifacts on disk. |
| **Inbound/OutboundNetworkEvents** | C2 beaconing (Outbound) or Scanning (Inbound). |
| **PassiveDns** | Mapping domains to IPs over time (Infrastructure pivots). |
| **ProcessEvents** | Execution chains (e.g., Word spawning PowerShell). |
| **SecurityAlerts** | High-level signals from EDR/firewalls. |

## 2. RegexOne (Tutorials 1-5)
**Status:** ✅ Completed
**Concepts Mastered:**
1.  **Matching Characters:** Exact string matching.
2.  **Wildcards (`.`):** Matching any single character (e.g., `file..txt`).
3.  **Matching Digits (`\d`):** Essential for parsing IPs and Ports.
4.  **Specific Characters:** Escaping special characters (like `\.` for a literal dot).
5.  **Excluding Characters (`[^...]`):** Filtering out noise.

## 🧠 Key Takeaway
Understanding KQL table structures allows me to know *where* to look during an incident. Regex allows me to *filter* that data when the standard fields aren't enough (e.g., extracting a specific malformed User-Agent string).