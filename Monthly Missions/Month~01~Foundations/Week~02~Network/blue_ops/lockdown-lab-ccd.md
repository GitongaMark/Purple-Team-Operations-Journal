# 🛡️ Blue Ops Report: CyberDefenders "Lockdown"
**Date:** February 2026
**Analyst:** Mark Gitonga
**Platform:** [CyberDefenders](https://cyberdefenders.org/)
**Category:** Network & Memory Forensics
**Scenario:** A Windows IIS server has been compromised. The goal is to analyze the PCAP and Memory Dump to identify the attacker and their malware.

---

## 1. Executive Summary
Investigated a confirmed breach of an IIS Web Server (`10.0.2.15`). The attacker (`10.0.2.4`) utilized SMB enumeration to upload a web shell (`shell.aspx`), establishing a reverse connection on port 4443. Persistence was achieved via a malicious startup item (`updatenow.exe`), identified as the **AgentTesla** RAT.

## 2. Phase 1: Network Forensics (Wireshark)
**Tools:** Wireshark, TShark

### 🕵️ Reconnaissance & Initial Access
* **Attacker Origin:** `10.0.2.4`
* **Technique:** T1046 (Network Service Discovery)
* **Enumeration:** Attacker probed specific SMB shares before the attack.
    * **Shares Accessed:** `\\10.0.2.15\Documents`, `\\10.0.2.15\IPC$`
    * *Filter:* `smb2.cmd == 3` (Tree Connect)

### 💥 Exploitation
* **Payload Delivery:** The attacker uploaded a malicious ASPX web shell.
    * **Filename:** `shell.aspx`
    * **Size:** 1,015,024 bytes
    * *Filter:* `smb2.cmd == 9` (Write Request)
* **Command & Control (C2) Channel:**
    * **Mechanism:** Reverse TCP Shell
    * **Port:** `4443` (Non-standard port to bypass basic firewall rules)
    * *Filter:* `ip.src == 10.0.2.15 && ip.dst == 10.0.2.4 && tcp.flags.syn == 1`

---

## 3. Phase 2: Memory Forensics (Volatility 3)
**Tools:** Volatility 3
**Dump Analysis:** `269-lockdown/memdump.mem`

### 🧠 System Context
* **Kernel Base Address:** `0xf80079213000`
    * *Command:* `python3 vol.py -f memdump.mem windows.info`

### 🦠 Process Analysis
* **Parent Process:** `w3wp.exe` (IIS Worker Process)
    * **PID:** `4332`
    * *Significance:* This confirms the attack originated from the web server application itself (webshell execution).
* **Malicious Child:** `updatenow.exe`
    * *Command:* `python3 vol.py -f memdump.mem windows.pstree`
    * *Observation:* `w3wp.exe` spawned an unfamiliar executable, which is highly suspicious behavior for a web server process.

### ⚓ Persistence
* **Mechanism:** Boot or Logon Autostart Execution (T1547)
* **Path:** `C:\ProgramData\Microsoft\Windows\StartMenu\Programs\Startup\updatenow.exe`
    * *Command:* `python3 vol.py -f memdump.mem windows.cmdline`

---

## 4. Phase 3: Malware Intelligence (OSINT)
**Tools:** VirusTotal, SHA256 Analysis

* **Malware Family:** **AgentTesla** (Commodity RAT / Infostealer)
    * *Hash:* `c25a6673a24d169de1bb399d226c12cdc666e0fa534149fc9fa7896ee61d406f`
* **Obfuscation:** Packed with **UPX** to evade static signatures.
* **C2 Domain:** `cp8nl.hyperhost.ua`

## 5. Key Takeaway
This lab highlighted the importance of **Correlation**. Wireshark showed *how* they got in (SMB -> Shell), but Volatility showed *what* they left behind (Persistence in Startup). Neither tool provided the full picture alone.