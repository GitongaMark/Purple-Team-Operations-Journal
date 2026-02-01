# 🏗️ The Purple Operations Lab: Infrastructure Build Journal

**Date:** February 2026
**Author:** Mark Gitonga
**Project:** Zero-to-Purple Architecture
**Status:** ✅ Operational

---

## 1. Executive Summary
This document details the technical deployment of a high-fidelity "Purple Team" home lab. The objective was to create an isolated yet monitored environment to simulate enterprise attack paths (Red Team) and develop detection engineering skills (Blue Team).

The infrastructure leverages **KVM/QEMU** on an Ubuntu host for near-native performance, with a centralized log aggregation pipeline using **Splunk Enterprise**.

## 2. Architecture Overview
| Node | OS / Role | IP Address | Key Software |
| :--- | :--- | :--- | :--- |
| **Host** | Ubuntu 22.04 LTS | `192.168.122.1` | Splunk Enterprise (SIEM), Autopsy (Forensics), KVM Hypervisor |
| **Victim** | Windows 10 Enterprise | `192.168.122.x` | Sysmon v15, Splunk Universal Forwarder, FTK Imager |
| **Attacker** | Kali Linux | `192.168.122.y` | Sliver C2, Ligolo-ng, Metasploit, Nmap |

---

## 3. Phase 1: The Hypervisor Stack (KVM)
**Goal:** Deploy a Type-1 equivalent hypervisor on Linux for maximum performance.

### 3.1 Installation
Switched from VirtualBox to KVM (Kernel-based Virtual Machine) to utilize the Linux kernel directly.

```bash
# Install KVM stack and GUI manager
sudo apt install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virt-manager

# Add user to libvirt group (prevents permission errors)
sudo usermod -aG libvirt $USER
sudo usermod -aG kvm $USER
# REBOOT REQUIRED
```

### 3.2 The Windows Driver Challenge (VirtIO)
Windows 10 does not natively support KVM's high-speed paravirtualized hardware.

* **Solution:** Downloaded the `virtio-win.iso` driver pack from the Fedora Project.
* **Implementation:** During Windows installation, I mounted this ISO to load the **Red Hat VirtIO SCSI Controller**, allowing the installer to see the virtual hard disk.

---

## 4. Phase 2: The Victim Configuration (Windows 10)
**Goal:** A realistic, telemetry-rich endpoint.

### 4.1 Solving the "No Internet" Issue
Post-installation, the Windows VM had no network connectivity.

* **Diagnosis:** Windows lacked the driver for the **VirtIO Ethernet Adapter**.
* **Fix:**
    1. Opened Device Manager (Yellow warning triangle on Ethernet Controller).
    2. Selected **Update Driver** -> **Browse my computer**.
    3. Pointed to the `virtio-win` ISO drive.
* **Result:** Internet connectivity established immediately.

### 4.2 Sysmon Deployment (The Eyes)
Standard Windows Event Logs are insufficient for detecting modern threats. I deployed Sysmon with the SwiftOnSecurity configuration.

* **Hurdle:** Running `Sysmon64.exe -i` failed because the config file was missing locally.
* **Fix:** Downloaded the raw config via PowerShell:

```powershell
Invoke-WebRequest -Uri [https://raw.githubusercontent.com/SwiftOnSecurity/sysmon-config/master/sysmonconfig-export.xml](https://raw.githubusercontent.com/SwiftOnSecurity/sysmon-config/master/sysmonconfig-export.xml) -OutFile sysmonconfig-export.xml

### Installation
```cmd
Sysmon64.exe -i sysmonconfig-export.xml -accepteula
```
## 5. Phase 3: The Logging Pipeline (Splunk)
**Goal:** Centralized ingestion of Windows logs into the Ubuntu Host.

### 5.1 Host-Side Setup (Ubuntu)
Installed Splunk Enterprise directly on the host to save VM resources.

* **Config:** Enabled listening on TCP Port `9997` (Forwarding Port).
* **Firewall Check:** Verified port status using:
  ```bash
  sudo ss -tulpn | grep 9997

### 5.2 Agent Setup (Windows)
Installed the **Splunk Universal Forwarder (UF)**.
* **Target:** Configured to send logs to `192.168.122.1:9997`.

### 5.3 Troubleshooting Connectivity (The "Silent" Log Issue)
**Problem:** The Forwarder was installed, and Pings were successful, but no data appeared in Splunk.

**Root Cause Analysis:**
1.  **Layer 3 Check:** `ping 192.168.122.1` → **Success**.
2.  **Layer 4 Check:** `Test-NetConnection -ComputerName 192.168.122.1 -Port 9997` → **Success**.
3.  **Application Layer:** Checked `splunkd.log` and found the Forwarder was idle. The `inputs.conf` file was empty, meaning the agent was connected but had "nothing to say."

**Resolution:**
Manually configured `C:\Program Files\SplunkUniversalForwarder\etc\system\local\inputs.conf`:

```ini
[default]
host = Windows-Victim

[WinEventLog://Application]
disabled = 0
[WinEventLog://Security]
disabled = 0
[WinEventLog://System]
disabled = 0
[WinEventLog://Microsoft-Windows-Sysmon/Operational]
disabled = 0
```

### Service Restart
* **Encountered:** `System error 5` (Access Denied).
* **Fix:** Ran Command Prompt as **Administrator** before executing `net stop SplunkForwarder`.

### Verification
* **Result:** Splunk Search `index=*` now shows live Sysmon events from the victim.

## 6. Phase 4: Forensic & Offensive Tooling

### 6.1 Forensics (Dead Disk Analysis)
* **FTK Imager:** Downloaded `FTK Imager Lite 4.7.1.exe` (standalone) instead of the massive FTK Suite ISO to save space.
* **Autopsy:** Installed on Ubuntu Host.
  * **Issue:** Snap version (`/snap/bin/autopsy`) had path/sandbox issues.
  * **Resolution:** Switched to the official `.zip` release for direct disk access.

### 6.2 Offensive Tools (Kali Linux)
* **Sliver C2:** Installed binary server for Command & Control.
* **Ligolo-ng:** Configured TUN interface for pivoting scenarios.

```bash
sudo ip tuntap add user kali mode tun ligolo
sudo ip link set ligolo up
```
### 6.3 Quality of Life (Copy/Paste)
Enabled **SPICE Guest Agents** to allow clipboard sharing between Ubuntu, Kali, and Windows.

* **Kali:**
  ```bash
  sudo apt install spice-vdagent

* **Windows:** Installed `virtio-win-guest-tools.exe` from the driver ISO.