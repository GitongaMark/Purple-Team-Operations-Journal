# 🦈 Wireshark Refresher Course
**Date:** 2026-02-10

**Instructor:** Chris Greer

**Topic:** Packet Capture, Network Forensics, and PCAP Analysis

## 1. Executive Summary
Completed an intensive masterclass on Wireshark. The focus was on moving beyond basic packet viewing to configuring Wireshark as a forensic workstation, optimizing capture methodologies, and extracting malicious artifacts directly from network traffic.

## 2. Environment Setup & Customization
Default Wireshark is noisy. I configured the interface for speed and clarity.
* **Configuration Profiles:** Created dedicated profiles (e.g., "HTTP Analysis", "TCP Troubleshooting") to switch column layouts instantly.
* **Delta Time Column:** Added `frame.time_delta_displayed` to see the gap between packets.
    * *Why:* It allows instant spotting of network latency (Server delay vs. Network delay).
* **Name Resolution:**
    * Configured **GeoLite2** database to map IP addresses to physical locations (Country/City).
    * Manually resolved IPs to Hostnames (e.g., mapping `192.168.1.50` to `HR-FILE-SERVER`).

## 3. Capture Strategy (The "Where" and "How")
Learned that placement is everything. You cannot analyze what you cannot see.
| Method | Pros | Cons |
| :--- | :--- | :--- |
| **Client-Side** | Easy to execute. Sees traffic exactly as the user sees it. | Misses infrastructure issues. |
| **SPAN / Mirror Port** | Captures switch-wide traffic. No impact on link. | Can drop packets if bandwidth exceeds port speed. |
| **Network TAP** | 100% fidelity. Impossible to drop packets. | Requires physical hardware installation. |

* **Long-Term Capturing:**
    * Used **`dumpcap`** from the command line (added to PATH) instead of the Wireshark GUI to save system resources.
    * Configured a **Ring Buffer**: This allows for continuous, long-term traffic capture by writing to a set of files that overwrite the oldest data when the limit is reached, preventing the hard drive from filling up.

## 4. Filtering (Signal vs. Noise)
Mastered the difference between **BPF (Capture Filters)** and **Display Filters**.
* **Capture Filters:** `host 192.168.1.10 and port 80` (Discards everything else *before* saving).
* **Display Filters:** `ip.addr == 192.168.1.10 && http` (Hides noise but keeps data).
* **Search:** Used `frame contains "password"` to hunt for cleartext credentials in the payload.

### 5. Advanced Forensics & Artifact Extraction
* **Wireshark Statistics:** Used built-in stats to get a high-level overview of the PCAP (top talkers, endpoints, conversations) before diving into individual packets.
* **Extracting Files:** * Used **Follow TCP Stream** to read the exact back-and-forth dialogue between a client and server.
    * Used **Export Objects** to carve actual files (e.g., HTTP downloads, malware payloads) directly out of the PCAP to save to disk.
* **Geolocation:** Integrated the **GeoLite2** free database to map IP addresses to physical locations, a key step in identifying anomalous inbound/outbound connections.

## 🧠 Key Takeaway
Wireshark is not just a protocol analyzer; it is an artifact extraction engine. Mastering `dumpcap` with ring buffers ensures the SOC never misses an event due to storage limits, and knowing how to export objects allows analysts to safely reconstruct malware dropped over the wire.