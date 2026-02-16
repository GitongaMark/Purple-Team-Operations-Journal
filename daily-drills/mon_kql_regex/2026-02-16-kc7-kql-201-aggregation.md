# 🟦 Daily Drill: KQL 201 (Aggregation & Timelines)
**Date:** 2026-02-16
**Source:** KC7 Cyber
**Topic:** KQL Aggregation, Distinct Counts, and Time-Based Functions

## 1. Executive Summary
Advanced from basic KQL querying to data transformation using the `summarize` operator. This drill focused on aggregating massive datasets to identify patterns, such as counting brute-force login attempts, tracking the spread of malicious links, and establishing the exact timeline of a ransomware encryption event.

## 2. Core Aggregation (The `summarize` Operator)
Unlike `where` (which keeps the table structure), `summarize` **transforms** the table. It collapses thousands of rows into a single summary row based on the groups you define.

| Function | KQL Syntax | DFIR Use Case |
| :--- | :--- | :--- |
| **`count()`** | `summarize count() by process_name` | Finds the most frequently executed processes. Good for spotting noisy malware. |
| **`dcount()`** | `summarize unique_accounts = dcount(username) by src_ip` | **Distinct Count.** Identifies Password Spraying attacks (one IP trying many unique usernames). |
| **`top`** | `top 10 by execution_count` | Combines `sort by desc` and `take 10` into a single, highly performant command. |

## 3. Timeline Analysis (Establishing the Kill Chain)
In incident response, knowing *when* something happened is as important as *what* happened. KQL uses mathematical functions on timestamps to build timelines.

* **`min(timestamp)`:** Finds the earliest record (First Seen).
* **`max(timestamp)`:** Finds the latest record (Last Seen).

**Practical Example: Ransomware Tracking**
Tracking exactly when files with the `.encrypted` extension were created on `ENRQ-LAPTOP` to determine the speed of the encryption routine:
```kql
FileCreationEvents
| where hostname == "ENRQ-LAPTOP"
| where filename endswith ".encrypted"
| summarize 
    first_seen = min(timestamp),
    last_seen = max(timestamp)
    by filename
| sort by first_seen asc
```
**Example: Detecting Brute Force**
```kql
AuthenticationEvents
| where result == "Failed Login"
| summarize total_fails = count() by src_ip
| sort by total_fails desc
```

**Example: Finding Password Spraying**
```kql
AuthenticationEvents
| where result == "Failed Login"
| summarize unique_targets = dcount(username) by src_ip
| where unique_targets > 10
```
---

## 4. Advanced Grouping

### 🧩 `make_set()`

Instead of counting, this creates a JSON array of all unique values.

**Use Case:**  
"Show me a list of all unique recipients for every phishing email subject."

```kql
| summarize recipients = make_set(recipient) by subject
```

### 🔟 `top`

A shortcut operator.

Instead of:

```kql
| sort by count_ desc
| take 10
```
