# 🧠 Daily Drill: Introduction to Splunk
**Date:** 2026-02-04
**Source:** education.splunk.com
**Topic:** Core Fundamentals & Visualization

## 1. Core Concepts
* **What is Splunk?** A "Data-to-Everything" platform that aggregates, indexes, and analyzes machine data (logs, sensors, streams) to provide operational intelligence.
* **Splunk Web:** The browser-based interface for managing the instance. Key areas include the "Apps" bar (left side) and the "Search & Reporting" app (the main workspace).

## 2. The Search Experience
* **Using Search:** The primary interface for querying data. The **Time Range Picker** is the most critical filter for performance (always limit the time window first!).
* **Search Terms:**
    * **Keywords:** Searching for literal strings (e.g., `error`, `failed`).
    * **Booleans:** `AND`, `OR`, `NOT` (Must be capitalized in SPL).
    * **Wildcards:** Using `*` to match partial strings (e.g., `fail*`).
* **Exploring Events:**
    * **Raw Text:** The original log message.
    * **Selected Fields:** High-value fields (Host, Source, Sourcetype) shown by default.
    * **Interesting Fields:** Fields that appear in at least 20% of the results.

## 3. Knowledge Objects & Reporting
* **Knowledge Objects:** User-defined entities that enrich data without changing the raw logs. Examples:
    * **Fields:** Extracted data points (e.g., `dest_ip`).
    * **Tags:** Aliases for field values (e.g., tagging `192.168.1.1` as `Firewall`).
* **Creating Reports:** A "Saved Search." Reports allow you to save a specific SPL query (e.g., "Weekly Failed Logins") to run on a schedule or embed in dashboards.

## 4. Visualizations
* **Creating Dashboards:** Collections of panels (charts/tables) that tell a story about the data.
* **Dashboard Studio:** The modern, pixel-perfect builder that offers more customization (images, free placement, JSON editing) compared to the "Classic" XML dashboards.

## 🧠 Key Takeaway
Splunk is not just a search engine; it is a **Time-Series Database**. Efficient searching requires understanding **Fields** (columns) and **Time** (rows). The goal of any analyst is to turn raw *Events* into *Reports*, and finally into *Dashboards* for management visibility.