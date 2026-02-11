# 🦈 Forensic Report: "Cold as Ice" (IcedID Infection)

**Date:** February 2026
**Analyst:** Mark Gitonga
**Source:** [Malware-Traffic-Analysis.net](https://www.malware-traffic-analysis.net/)
**Artifact:** Unit 42 Wireshark Quiz (IcedID -> RedLine Stealer)

---

## 1. Executive Summary
I conducted a packet-level forensic analysis of a network capture (`.pcap`) containing a simulated IcedID infection. The analysis confirmed that a Windows host was compromised on **July 10, 2023**, resulting in the deployment of **RedLine Stealer** malware. The attacker successfully attempted to exfiltrate sensitive browser data, cryptocurrency wallets, and user credentials.

## 2. Victim Identification (The "Who")
Using protocol analysis, I fingerprinted the infected endpoint to identify the specific machine and user account involved.

| Attribute | Value | Methodology |
| :--- | :--- | :--- |
| **IP Address** | `10.7.10[.]47` | Identified as the primary source of high-volume traffic. |
| **MAC Address** | `80:86:5b:ab:1e:c4` | Extracted from Ethernet frame headers (Source MAC). |
| **Hostname** | `DESKTOP-9PEA63H` | Filtered for **NBNS** (NetBIOS Name Service) traffic to find the machine name. |
| **User Account** | `rwalters` | Filtered for **Kerberos** authentication traffic (`kerberos.CNameString`) to find the active user. |

## 3. Incident Timeline (The "When")
* **Infection Start Time:** `2023-07-10 22:39 UTC`
* *Analyst Note:* Time display format was toggled to UTC to ensure standardized reporting and correlation with external threat intelligence.

## 4. Malware Behavior & Impact (The "What")
Deep packet inspection revealed the presence of **RedLine Stealer**, an infostealer malware often dropped by IcedID. The malware was observed attempting to harvest:
* **Browser Data:** Cookies, passwords, and history from Chrome, Edge, Opera, and Vivaldi.
* **Financial Assets:** Cryptocurrency wallet data and browser plugins associated with crypto management.
* **Credentials:** API keys and stored login credentials from desktop applications.
* **Files:** Recursively targeting `.txt`, `.doc`, and other document formats on the Desktop and Documents folders.

## 5. Key Forensic Filters Used
* `nbns` - To resolve Hostname.
* `kerberos.CNameString` - To resolve User Account.
* `frame.time` - To pinpoint the infection start.

## 6. Conclusion
The host `DESKTOP-9PEA63H` is confirmed compromised. Immediate isolation is required, followed by a password reset for user `rwalters` due to the confirmed exfiltration of credentials.