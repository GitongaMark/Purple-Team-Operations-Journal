# 📜 Daily Drill: Sigma Rules (Detection as Code)
**Date:** 2026-02-05
**Topic:** Sigma Rule Creation & Translation
**Focus:** AWS Cloud Security

## 1. Concept: What is Sigma?
Sigma is the "Rosetta Stone" of SIEM rules. It allows us to write a detection logic **once** in a generic format (YAML) and then translate it into the specific language of any SIEM (Splunk SPL, Elastic Query, Azure KQL, etc.). This is the industry standard for sharing threat intelligence.

## 2. The Rule: AWS Root Account Usage
**Objective:** Detect whenever the "Root" account is used in AWS.
**Why:** The Root account has unlimited access and cannot be restricted by IAM policies. In a secure environment, it should almost *never* be used for daily tasks. Active use usually indicates a breach or a serious policy violation.

### 📝 Sigma Rule (YAML)
```yaml
title: AWS Root Credentials
id: (Generate-UUID-Here)
status: experimental
description: Detects AWS root account usage in CloudTrail logs.
author: Mark Gitonga
date: 2026-02-05
logsource:
    product: aws
    service: cloudtrail
detection:
    selection:
        userIdentity.type: Root
    filter:
        eventType: AwsServiceEvent
    condition: selection and not filter
falsepositives:
    - AWS Tasks That Require Root User Credentials (e.g., Billing changes)
level: medium
```

## 3. The Translation (Splunk SPL)

Using tools such as **Uncoder.io** or the **sigmac compiler**, the generic Sigma rule above translates into the following Splunk query:

```spl
index=* userIdentity.type="Root" AND NOT eventType="AwsServiceEvent"
```

### 4. 🧠 Logic Breakdown

- **`userIdentity.type="Root"`**  
  Identifies events where the AWS **Root** account is used.

- **`NOT eventType="AwsServiceEvent"`**  
  Filters out backend AWS service events to reduce noise and focus on interactive or scripted Root usage.

## 5. 🎯 MITRE ATT&CK Mapping

### **Tactic**
- **Privilege Escalation**
- **Initial Access**
- **Persistence** (conditional, depending on usage context)

---

### **Technique**
- **T1078 – Valid Accounts**

---

### **Sub-Technique**
- **T1078.004 – Cloud Accounts**

---

### **Rationale**

The AWS **Root** account is a valid, highly privileged cloud account.  
Detection of Root usage aligns directly with **T1078.004 (Cloud Accounts)**, as adversaries frequently abuse compromised or mismanaged cloud credentials to gain or maintain access.

Because the Root account has unrestricted permissions:

- Its use may indicate **Initial Access** if credentials were newly compromised.
- It may signal **Privilege Escalation** if an attacker pivoted from a lower-privileged IAM user.
- Repeated or scripted use can suggest **Persistence**, especially when Root access is leveraged to create new IAM users, access keys, or disable security controls.

---

### **Defender Notes**

- Root account activity is **high-signal and low-noise**.
- Any non-billing Root usage should be investigated immediately.
- This detection is most effective when paired with alerts for:
  - IAM user creation
  - Access key generation
  - CloudTrail tampering