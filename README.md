# 🛡️ Purple Operations Journal

![Status](https://img.shields.io/badge/Status-Active_Development-brightgreen) ![Focus](https://img.shields.io/badge/Focus-Purple_Teaming-blueviolet) ![Author](https://img.shields.io/badge/Author-Mark_Gitonga-blue)

## 👤 About Me
I am a final-year **Computer Science & Mathematics** student at **Maseno University**, aspiring to bridge the gap between offensive security (Red Team) and defensive operations (Blue Team).

This repository documents my intensive 5-month "Purple Team" training roadmap. Unlike traditional study repositories, this journal focuses on **artifacts**: generating real attack traffic and building the exact detection rules required to stop it.

> **Current Status:** 🎓 Final Year Project (*CampusConnect*) & 🛡️ SOC Analyst Training

---

## 🧠 The Daily "Fingertips" Drill (30 Mins)
*Consistency beats intensity. I dedicate 30 minutes every single day to muscle memory.*

| Day | Domain | Activity | Directory Link |
| :--- | :--- | :--- | :--- |
| **Mon** | 🔍 Log Analysis | KQL Speed Games & Regex Patterns | [`/daily-drills/mon_kql_regex`](./daily-drills/mon_kql_regex) |
| **Tue** | 🐚 Scripting | PowerShell/Bash Piping & Grepping | [`/daily-drills/tue_powershell`](./daily-drills/tue_powershell) |
| **Wed** | 📊 SPL | Splunk Search Processing Language | [`/daily-drills/wed_splunk_spl`](./daily-drills/wed_splunk_spl) |
| **Thu** | 📐 Detection | Sigma Rules & MITRE Mapping | [`/daily-drills/thu_sigma_rules`](./daily-drills/thu_sigma_rules) |
| **Fri** | 🐧 Linux Ops | Linux CLI (Bandit) & Scripting | [`/daily-drills/fri_linux_bash`](./daily-drills/fri_linux_bash) |
| **Sat** | 📐 Detection | YARA Rules & MITRE Mapping | [`/daily-drills/sat_yara_rules`](./daily-drills/sat_yara_rules) |

---

## 🗺️ The 5-Month Operations Roadmap

### 📆 Month 1: Visibility & Foundations
| Mission | Red Action (Attack) | Blue Action (Defense) | Status |
| :--- | :--- | :--- | :--- |
| **Week 01** | Local Nmap Baseline | osTicket Setup & Doc | `[🔒 Locked]` |
| **Week 02** | EternalBlue Exploit | IcedID Packet Analysis | `[🔒 Locked]` |
| **Week 03** | Host PrivEsc (*Bratarina*) | Sysinternals Analysis | `[🔒 Locked]` |
| **Week 04** | Python Dropper Dev | Splunk Payload Detection | `[🔒 Locked]` |

### 📆 Month 2: Active Directory (Identity)
| Mission | Red Action (Attack) | Blue Action (Defense) | Status |
| :--- | :--- | :--- | :--- |
| **Week 05** | AD Enumeration (*Active*) | Domain Trust Mapping | `[🔒 Locked]` |
| **Week 06** | Kerberoasting | TGS-REQ Analysis | `[🔒 Locked]` |
| **Week 07** | Lateral Mvt (PsExec) | Persistence Hunting | `[🔒 Locked]` |
| **Week 08** | **CampusConnect Audit** | **WAF Log Analysis** | `[🔒 Locked]` |

### 📆 Month 3: The Full Stack (Web/Infra)
| Mission | Red Action (Attack) | Blue Action (Defense) | Status |
| :--- | :--- | :--- | :--- |
| **Week 09** | Double Pivot (Ligolo-ng) | Segmentation Testing | `[🔒 Locked]` |
| **Week 10** | Business Logic Bypass | Automated Alerting (Python)| `[🔒 Locked]` |
| **Week 11** | API BOLA Attack | CloudTrail Forensics | `[🔒 Locked]` |
| **Week 12** | External Recon | Attack Surface Mapping | `[🔒 Locked]` |

### 📆 Month 4: Advanced Threats
| Mission | Red Action (Attack) | Blue Action (Defense) | Status |
| :--- | :--- | :--- | :--- |
| **Week 13** | Exploit Modification | Static Analysis (Code) | `[🔒 Locked]` |
| **Week 14** | C2 Ops (Sliver) | YARA Rule Dev | `[🔒 Locked]` |
| **Week 15** | PowerShell Downloaders | Splunk Threat Hunt | `[🔒 Locked]` |
| **Week 16** | Malware Detonation | Sandbox Analysis (Any.Run)| `[🔒 Locked]` |

### 📆 Month 5: Capstone
| Mission | Red Action (Attack) | Blue Action (Defense) | Status |
| :--- | :--- | :--- | :--- |
| **Week 17** | Cloud Breach (IAM) | Cloud IR Timeline | `[🔒 Locked]` |
| **Week 18** | Simulated Adversary | Full IR Report | `[🔒 Locked]` |
| **Week 19** | **Red Team Final Exam** | **Purple Shift Detection**| `[🔒 Locked]` |

---

## 🎓 Featured Project: CampusConnect Security Audit
> *Integrated directly with my Final Year University Project.*

* **Target:** CampusConnect (Web Platform for Maseno University)
* **Methodology:** Grey-box Penetration Test & Secure Code Review.
* **Key Finding:** [To be added - e.g., Stored XSS in Chat Module]
* **Remediation:** [To be added - e.g., Implemented CSP and Input Sanitization]
* **[🔗 Read the full Audit Report](./campus-connect-audit/pentest-report.md)**

---

## 🛠️ Tools & Technologies
* **SIEM/Log Analysis:** Splunk (SPL), Elastic (KQL), Sysmon
* **Network Forensics:** Wireshark, Zeek, Brim
* **Offensive:** Metasploit, Burp Suite, Nmap, Sliver C2, Ligolo-ng
* **Languages:** Python, Bash, PowerShell
* **Cloud:** AWS (CloudTrail, IAM)

---

## ⚠️ Disclaimer
*All information in this repository is for educational purposes only. Attacks are simulated in a controlled, isolated lab environment or against systems I own/have explicit permission to test (CampusConnect).*