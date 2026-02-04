# 🚩 HackTheBox Writeup: Dancing

**Date:** February 4, 2026
**Target IP:** 10.129.112.84
**Difficulty:** Very Easy
**OS:** Windows
**Vulnerability:** Unsecured SMB Shares (Anonymous Access)

---

## 1. Executive Summary
During an authorized security assessment of the target "Dancing," a critical misconfiguration was identified in the Server Message Block (SMB) protocol. The host allowed anonymous (unauthenticated) users to list and access sensitive file shares. This vulnerability allowed the retrieval of sensitive data (the User Flag) without requiring valid credentials or an exploit code.

## 2. Reconnaissance (Scanning)
The engagement began with a network scan to identify open ports and services.

**Command:**
```bash
sudo nmap -sC -sV -p- 10.129.x.x
```

## Findings
*   **Port 445 (TCP):** microsoft-ds (SMB) - **OPEN**
*   **Port 139 (TCP):** netbios-ssn - **OPEN**
*   **Port 135 (TCP):** msrpc - **OPEN**

> **Note:** The presence of Port 445 indicates the server is likely a file server.

---

## 3. Enumeration (The Discovery)
I attempted to list the available shares using `smbclient` to determine if the server permitted listing without a password.

**Command:**
```bash
smbclient -L 10.129.x.x
# (Hit Enter when prompted for a password to attempt empty login)
```
### Output
| Share Name | Type | Description |
| :--- | :--- | :--- |
| ADMIN$ | Disk | Remote Admin |
| C$ | Disk | Default Share |
| IPC$ | IPC | Remote IPC |
| WorkShares | Disk | |

**Analysis:** While `ADMIN$` and `C$` are standard administrative shares (usually locked), **WorkShares** appeared to be a custom user share.

---

### 4. Exploitation (Access & Retrieval)
I attempted to connect to the custom share **WorkShares** using an anonymous session.

**Command:**
```bash
smbclient //10.129.x.x/WorkShares
```

### Action Log

*   **Access Granted:** Successfully entered the `smb: \>` prompt.
*   **Navigation:** Used `ls` to list directories.
*   **Discovery:** Found two directories: `Amy.J` and `James.P`.

#### Exploration
*   **Amy.J:** Contains `worknotes.txt` (irrelevant).
*   **James.P:** Found `flag.txt`.

#### Exfiltration
```bash
cd James.P
get flag.txt
```

**Outcome:** The file `flag.txt` was successfully downloaded to the local attacker machine.

---

### 5. Remediation (Blue Team Defense)
To prevent this vulnerability in a production environment:

*   **Disable Anonymous Listing:** Configure the `LanmanServer` registry key to restrict anonymous enumeration of shares.
*   **Restrict Permissions:** Ensure that sensitive shares like `WorkShares` explicitly require authenticated users (Active Directory groups) and deny the `Everyone` or `Anonymous Logon` groups.
*   **Firewalling:** Block Port 445 access from untrusted networks (Internet).

---

### 6. Key Takeaways
*   **SMB is a Goldmine:** Port 445 is often the easiest entry point into a Windows environment.
*   **Enumeration is Key:** Simply listing shares (`-L`) revealed the path. No complex exploit was needed.
*   **Misconfiguration > Exploits:** Most breaches happen due to bad settings (permissions), not zero-day exploits.