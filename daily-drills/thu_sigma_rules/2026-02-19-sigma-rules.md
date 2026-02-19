# 📜 Daily Drill: Sigma Rule Fundamentals
**Date:** 2026-02-19
**Source:** sigmahq.io Documentation
**Topic:** YAML Architecture, Detections, and Metadata

## 1. The Core Architecture
Sigma rules are YAML files containing all the information required to detect odd, bad, or malicious behavior in log files, usually within the context of a SIEM. 

Every Sigma rule is divided into three primary components:
* **Detection:** Specifies exactly what malicious behavior the rule is searching for across relevant logs.
* **Logsource:** Defines the specific types of logs this detection should search over.
* **Metadata:** Contains supplementary information about the detection, such as tags, author, and severity level.

## 2. The Detection Section (The Engine)
This is the most critical component. Sigma organizes detection logic into groups called "selections".

### AND / OR Logic
* **OR Operations:** Represented by YAML **Lists** (using dashes `-`).
* **AND Operations:** Represented by YAML **Dictionaries/Objects** (key-value pairs).

### Detection Methods
There are three main patterns for composing detections:

1. **By Keyword:**
   * Searches for strings within the raw log. 
   * Each item in a `keywords` list acts as a logical "OR". 
   * *Note:* While easy to write, most SIEMs perform vastly better using field-based searches rather than keyword searches.
2. **By Field Value:**
   * Uses a YAML object to define specific field-value pairs (e.g., `Username: "Administrator"`). 
   * Multiple fields within the same selection object are treated as an "AND" condition.
3. **By Field List:**
   * Used to search for multiple values within a single field.
   * Represented by a YAML list under a specific field name, which translates to an `IN (...)` operator in SPL.

### Conditions
Every selection group must be tied together in the `condition` field at the bottom of the detection section. Conditions organize, combine (using `and`/`or`), or negate (using `not`) selection groups to perform powerful detection operations.

## 3. Logsource Definition
The logsource section targets specific logs so the SIEM doesn't waste resources searching everything. It uses three main fields:
* **Category:** A category of products (e.g., `webserver`, `edr`).
* **Product:** A specific product (e.g., `windows`, `linux`).
* **Service:** A specific service running within the product (e.g., `kerberos`).

## 4. Essential Metadata
While some metadata is optional, these are the standards you will see and use daily:

* **ID:** A randomly generated UUIDv4 that globally identifies the rule. It should only change if the rule logic undergoes major changes, merges, or forks.
* **Title:** A very short summary of the rule's goal. Titles should avoid prefixes like "Detects when...".
* **Status:** Can be `stable`, `test`, `experimental`, `deprecated`, or `unsupported`.
* **Tags:** Maps the rule to frameworks like MITRE ATT&CK, CVEs, or Traffic Light Protocol (TLP).
* **False Positives:** A list of known benign triggers to help analysts triage the alert.
* **Level:** Dictates criticality (`informational`, `low`, `medium`, `high`, `critical`). 
    * *Critical/High:* Indicates an incident requiring immediate manual review.
    * *Medium/Low/Informational:* Indicates suspicious activity, policy violations, or compliance data usually suited for dashboards.