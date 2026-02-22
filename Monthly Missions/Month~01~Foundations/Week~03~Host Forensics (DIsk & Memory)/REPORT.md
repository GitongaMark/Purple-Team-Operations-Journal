# 🔎 Week 3 Report: Forensic Acquisition & Deleted File Recovery
**Date:** 2026-02-22
**Category:** Host Forensics (Disk & Memory)
**Author:** Mark Gitonga

## 1. Executive Summary
This week, I conducted a full-cycle "Purple Team" exercise focused on Host Forensics. I started by compromising a machine (HTB: Devel) and applying anti-forensics techniques like file deletion and timestomping. I then pivoted to the Blue Team side to perform a cryptographically sound disk acquisition, analyze the raw Hex, and successfully carve the deleted data directly from the disk image.

## 2. 🟥 Red Ops: Anti-Forensics & Evasion
My goal on the Red side was to simulate a threat actor attempting to cover their tracks after looting the machine.

* **Initial Access Warm-up:** Exploited the Hack The Box (HTB) machine **Devel**.
* **Data Destruction:** Intentionally deleted a highly sensitive file named `passwords.txt` from the filesystem to simulate data destruction/hiding.
* **Timestomping:** Used the Meterpreter `timestomp` module to manipulate the MACB (Modified, Accessed, Created, Birth) timestamps of files. 
    * *Why:* This is a classic anti-forensics technique designed to break the Blue Team's timeline analysis, making it look like a file was created years ago rather than during the actual breach window.

## 3. 🟦 Blue Ops: Acquisition & Recovery
Operating as the forensic investigator, I ignored the live operating system and went straight for the raw disk data to bypass the Red Team's tricks.

### Step 1: Evidence Acquisition (FTK Imager)
* **Action:** I used **FTK Imager** (Windows) to create a bit-for-bit physical image of the target drive.
* **Format:** Exported as an **E01** (EnCase Image Format).
* *Forensic Value:* The E01 format hashes the data during imaging and compresses it, ensuring the evidence is cryptographically sound and admissible in an investigation. It prevents any accidental writes to the evidence.

### Step 2: Data Carving (Foremost & Autopsy)
* **Action:** I moved the E01 image into my Linux forensic environment and loaded it into **Autopsy**. 
* **Recovery:** Since standard deletion only removes the pointer in the `$MFT` (Master File Table) and leaves the actual data on disk, I used **Foremost** to carve the unallocated space. 
* *Result:* Successfully recovered the "deleted" `passwords.txt` file.

### Step 3: Hex Analysis & Magic Bytes
* **Action:** To verify the integrity of the recovered files and hunt for disguised malware, I analyzed the files in a Hex viewer.
* **File Signatures:** I learned to identify files not by their easily spoofed extensions (e.g., `.txt`), but by their **Magic Bytes** (e.g., `4D 5A` for a Windows executable or `25 50 44 46` for a PDF) located at the very beginning of the file header.

## 4. Theory & Architecture
To understand *why* these forensic artifacts exist, I spent time reading through the **Windows Internals (Sysinternals)** documentation. Understanding how the NT kernel manages memory, threads, and the NTFS file system is critical for knowing where malware hides and how data persists after deletion.

## 🧠 Key Takeaway
"Deleting" a file doesn't actually delete the data; it just tells the OS that the space is available to be overwritten. Timestomping can fool a basic file explorer, but conducting Hex-level analysis and data carving on a raw E01 image reveals the absolute truth of what happened on the disk.