# 🟦 Daily Drill: KQL 201 (Data Transformation & Filtering)

**Date:** 2026-02-23  
**Source:** KC7 Cyber  
**Topic:** KQL Data Transformation and Advanced Filtering  
**Author:** Mark Gitonga  

---

## 1. Executive Summary

Today, I completed the second half of the KQL 201 module on KC7. My focus shifted from aggregating data to transforming and enriching it. I learned how to sculpt the output of my queries using projection and extension, apply conditional logic directly in the pipeline, and perform advanced string and list filtering to hunt for specific threat indicators.

---

## 2. Data Transformation (`project` vs `extend`)

Understanding how to shape the output table is critical for building clean dashboards and reports.

| Operator | Function | My Notes |
| :--- | :--- | :--- |
| **`project`** | Selects, renames, or drops columns. | **Replaces** the existing table. If I only want to see 3 specific columns out of 50, I use `project`. |
| **`extend`** | Creates calculated columns. | **Keeps** everything. It appends the new calculated column to the end of the existing dataset without deleting the original data. |

### Example:

```kql
// Renaming a column while discarding the rest
SecurityEvent 
| project TimeGenerated, TargetUser = TargetUserName

// Adding a new column while keeping the rest
SecurityEvent 
| extend AccountDomain = split(TargetUserName, "\\")[0]
```
---

## 3. Conditional Logic

I learned how to build "If/Then" logic directly into my queries to tag suspicious activity on the fly.

### `iff()`

Adds simple conditional logic.

**Syntax:**
```kql
iff(condition, value_if_true, value_if_false)
```
### Example:

```kql
extend IsAdmin = iff(Username contains "admin", "Yes", "No")
```
### `case()`

Used when I have multiple, complex conditions to evaluate sequentially (like a switch statement in programming).

**Example:**

```kql
SecurityEvent
| extend LogonTypeLabel = case(
    LogonType == 2, "Interactive",
    LogonType == 3, "Network",
    LogonType == 10, "RemoteInteractive",
    "Other"
)
```
---

## 4. Advanced Filtering (String & List Matching)

I expanded my filtering arsenal beyond simple `==` and `!=` operators.

---

### Pattern Matching

- **`startswith`**: Finds values that begin with a specific string  
  *(e.g., finding all processes that start with `powershell`).*

- **`endswith`**: Finds values that end with a specific pattern  
  *(e.g., hunting for ransomware extensions like `endswith ".encrypted"`).*

---

### List Matching (`in` vs `has_any`)

This is a crucial performance distinction for threat hunting:

- **`in` / `!in`**: Checks if an exact value exists within a provided list.  

  **Example:**
  ```kql
  where EventID in (4624, 4625)
  ```
### `has_any`

Looks for partial/token matches from a list within a larger string.

**Example:**
```kql
where CommandLine has_any ("mimikatz", "dumpcreds", "bypass")
```
---

## 🧠 Key Takeaway

The `extend` operator combined with `iff()` is incredibly powerful. Instead of just searching for bad activity, I can write a query that evaluates every log and automatically tags it with a **"Risk Score"** or **"Threat Type"** column, making the final output much easier to analyze.