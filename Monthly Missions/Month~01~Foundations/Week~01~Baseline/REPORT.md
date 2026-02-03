# 📝 Week 1: Infrastructure Baseline Analysis

## 1. Red Team Operations
Executed Nmap scans against Windows 10 Target (`192.168.122.191`).
* **Command:** `nmap -A -p- 192.168.122.191`
* **Findings:**
![Alt Text]![Nmap Scan](red_ops/nmap%20scan.png)

    * *Port 135 (RPC) - Open :*
    This port is used for Microsoft Remote Procedure Call (RPC) and the Endpoint Mapper. It allows client applications to discover the location of specific services on a network.

    * *Port 139 (NetBIOS) - Open :*
    Used for NetBIOS session services, primarily for legacy file and printer sharing in older Windows systems.

    * *Port 445 (SMB) - Open :*
    The modern Server Message Block (SMB) port, used for direct file sharing over TCP/IP, bypassing the older NetBIOS layer.

    * *Port 5357 (WSDAPI) - Open :*
    Used by Web Services for Devices (WSDAPI) to discover and interact with devices like printers and scanners on the local network.

    * *Suspicion:* The target is exposing core Windows networking and discovery services. The combination of 135, 139, and 445 suggests a legacy or improperly hardened Windows environment vulnerable to "wormable" exploits like EternalBlue (WannaCry) or remote code execution via malformed RPC/WSD headers.

## 2. Blue Team Operations
Checked Splunk for traces of the scan.
* **Query Used:** `index=* EventCode=3 SourceIp="192.168.122.20"`
* **Observation:** Sysmon Event ID 3 successfully captured the high volume of connection attempts from the Kali IP.
* **Gaps:** The standard Windows Firewall Logs were silent (default setting). Only Sysmon provided visibility.