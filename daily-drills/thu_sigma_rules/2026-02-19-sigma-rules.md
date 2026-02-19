# 📜 Daily Drill: Sigma Rule Fundamentals

**Date:** 2026-02-19  
**Source:** sigmahq.io Documentation  
**Topic:** YAML Architecture, Detections, and Metadata  
**Author:** Mark Gitonga

## 1. Executive Summary
Today, I decided to step back and learn the foundational basics of Sigma rules directly from the documentation. I realized I wasn't fully grasping the rules I was writing, so I needed to understand the underlying YAML architecture first. I started with the core rule structure today, and next week I will move on to modifiers. I focused on capturing the most practical, everyday information required to detect malicious behavior in a SIEM.

## 2. The Core Architecture
I learned that Sigma rules are essentially YAML files broken down into three main components:
* **Detection:** What malicious behavior I am searching for.
* **Logsource:** What types of logs I should search over.
* **Metadata:** Extra context about the detection (tags, author, etc.).

## 3. The Detection Engine (What I learned about Logic)
The detection section is the most important part. I discovered that Sigma categorizes detections into groups called "selections." 

I also mapped out how Sigma handles logical operators using YAML:
* **OR Operations:** I represent these using YAML **Lists** (with a `-`).
* **AND Operations:** I represent these using YAML **Dictionaries/Objects** (key-value pairs).

### Detection Methods I Explored
1.  **By Keyword:** I can search for raw strings (like `'rm *bash_history'`). While easy to write, I noted that field-based searches perform vastly better in most SIEMs.
2.  **By Field Value:** I can define a specific field and value (e.g., `Username: "Administrator"`). If I list multiple fields in one selection, Sigma treats it as an "AND".
3.  **By Field List:** If I want to search for multiple Event IDs (like 4728, 4729, 4730), I can use a YAML list under the `EventID` field.

Every selection group I make must be tied together in the `condition` field at the bottom (e.g., `condition: selection_one and not filter`).

## 4. Logsource Definition
To prevent the SIEM from wasting resources, I learned to define exactly where the rule should look using three fields:
* **Category:** The product type (e.g., `webserver`).
* **Product:** The specific product (e.g., `windows`).
* **Service:** The specific service (e.g., `security` or `sysmon`).

## 5. Metadata Standards
I reviewed the standard metadata fields to ensure my future rules look professional:

| Field | My Notes / Best Practices |
| :--- | :--- |
| **ID** | Must be a randomly generated UUIDv4. I should only change it if I heavily modify the rule logic. |
| **Title** | Keep it short. Avoid using prefixes like "Detects when...". |
| **Status** | Can be `stable`, `test`, `experimental`, `deprecated`, or `unsupported`. |
| **False Positives** | A list of known benign triggers to help the analyst (me) triage the alert later. |
| **Level** | Defines criticality. *Critical/High* means an incident requiring review. *Medium/Low/Informational* indicates suspicious activity or policy violations. |
| **Tags** | Used to map my rule to frameworks like MITRE ATT&CK or CVEs. |

## 🧠 Key Takeaway
Jumping straight into writing advanced SIEM rules without understanding YAML lists vs. dictionaries was holding me back. By breaking down the anatomy of a Sigma rule today, I now understand exactly how the compiler translates my `selection` blocks into SPL.