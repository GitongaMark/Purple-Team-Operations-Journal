# 🟦 Daily Drill: PowerShell for DFIR (TCM Academy)
**Date:** 2026-02-17  
**Topic:** Live Response, System Enumeration & Artifact Collection  
**Focus:** Utilizing native PowerShell cmdlets and WMI/CIM for forensic triage.  

## 1. Data Shaping & Output
Before collecting artifacts, it is critical to know how to format and export the data without losing fidelity.

| Cmdlet / Alias | Purpose |
| :--- | :--- |
| `Format-Table (ft)` / `Format-List (fl)` | Changes console display for readability. `fl` is best for deep dives into single objects. |
| `Select-Object (select)` | Isolates specific properties (columns) from an object. |
| `Where-Object` | Filters data based on conditions (e.g., `Where-Object {$_.Status -eq "Running"}`). |
| `Sort-Object` / `Group-Object` | Organizes data for anomaly detection. |
| `ConvertTo-Json` / `ConvertTo-Csv` | Formats data for ingestion into other tools. |
| `Export-Csv -NoTypeInformation` | Saves data to disk cleanly without PowerShell header metadata. |

## 2. Core DFIR Collection Playbook
During an incident, the goal is to collect volatile data (memory, network) before non-volatile data (disk).

### 🧠 System & User Context
* **OS Info:** `Get-CimInstance Win32_OperatingSystem | Select-Object LastBootUpTime`
* **Remote Collection:** `Get-CimInstance -ClassName Win32_OperatingSystem -ComputerName WKS-4F1D`
* **Logged-on Users:** `Get-CimInstance -ClassName Win32_LoggedOnUser`

### ⚙️ Execution (Processes & Scheduled Tasks)
Malware must run to be effective. These commands hunt for malicious execution.
* **Process Deep Dive:** `Get-CimInstance -ClassName Win32_Process | Select-Object Name, ProcessId, ParentProcessId, ExecutablePath, CommandLine`
* **Process Hashing:** `Get-Process | Where-Object {$_.Path} | ForEach-Object {Get-FileHash $_.Path}`
* **Suspicious Tasks:** `Get-ScheduledTask | Where-Object {$_.State -ne "Disabled"}`
* **Recently Run Tasks:** `Get-ScheduledTask | Where-Object {($_.State -ne "Disabled") -and ($_.LastRunTime -gt (Get-Date).AddDays(-7))}`

### 🌐 Network Connections
* **Active Connections:** `Get-NetTCPConnection -State Established`
* **UDP Endpoints:** `Get-NetUDPEndpoint | Select-Object LocalAddress, LocalPort, OwningProcess`
* **DNS Cache:** `Get-DnsClientCache` (Critical for finding C2 beaconing domains).

### ⚓ Persistence Mechanisms (Autoruns & Services)
Attackers modify these locations to survive a reboot.
* **Registry Run Keys:**
  * `Get-ItemProperty -Path "HKLM:\Software\Microsoft\Windows\CurrentVersion\Run"`
  * `Get-ItemProperty -Path "HKLM:\Software\Microsoft\Windows\CurrentVersion\RunOnce"`
* **Services:** `Get-CimInstance -ClassName Win32_Service | Select-Object Name, DisplayName, State, StartMode, PathName`

### 📂 File System, Evidence of Execution & Evasion
* **USB History:** `Get-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Enum\USBSTOR"`
* **Recent Files/Prefetch:**
  * `Get-ChildItem "$env:APPDATA\Microsoft\Windows\Recent"`
  * `Get-ChildItem C:\Windows\Prefetch` (Proves an executable actually ran).
* **Defender Exclusions:** `Get-MpPreference | Select-Object -Property ExclusionPath, ExclusionExtension, ExclusionIpAddress, ExclusionProcess` (Attackers add their malware to this list).
* **Shadow Copies:** `Get-CimInstance Win32_ShadowCopy` (Checking if backups exist before ransomware strikes).

## 3. Security Event Logs
Filtering event logs directly from the command line for specific TTPs (Tactics, Techniques, and Procedures).
* **Account Creation/Modification:**
  ```powershell
  Get-WinEvent -FilterHashtable @{LogName="Security"; ID=4720,4722,4724,4738,4732,1102}

## 🧠 Key Takeaway
Mastering `Get-CimInstance` unlocks the entire Windows Management Instrumentation (WMI) database. Combining CIM queries with Registry reads (`Get-ItemProperty`) allows a defender to script a complete forensic triage collection without needing to install any third-party agents on the compromised host.