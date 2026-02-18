# 🟠 Daily Drill: Splunk Visualizations & Transformations
**Date:** 2026-02-18
**Source:** education.splunk.com
**Topic:** Data Formatting, Transforming Commands, and Visual Mapping

## 1. Executive Summary
Completed the Splunk Visualization module, transitioning from raw log filtering to data presentation. The core focus was on transforming commands (`stats`, `chart`, `timechart`) that aggregate data for visual dashboards, as well as the syntax required to format that data cleanly.

## 2. Formatting Commands (Data Preparation)
These commands do not run statistics; they clean up how the data looks on the screen.

| Command | Usage / Concept |
| :--- | :--- |
| **`fields`** | `\| fields - _raw` removes the raw log block. `\| fields user, ip` keeps only those columns. |
| **`table`** | `\| table Time, User, IP, Action` forces the output into a strict column order. |
| **`dedup`** | Removes duplicate rows based on a specific field. Essential for cleaning noisy logs. |
| **`addtotals`** | Appends a row or column that sums up numeric values. |
| **`fieldformat`** | Changes how a value *looks* without changing its underlying value (e.g., formatting raw seconds into `MM:SS` while letting Splunk still do math on the raw seconds). |

## 3. Transforming Commands (The Visualization Engine)
Transforming commands are required to build charts and graphs. They convert raw events into statistical tables.

| Command | Description | Best Used For |
| :--- | :--- | :--- |
| **`stats`** | Calculates aggregate statistics (count, sum, avg) over the entire dataset. | Single value panels or standard data tables. |
| **`chart`** | Creates a data table with an X-axis and a Y-axis. | Bar, Line, or Pie charts comparing two variables. |
| **`timechart`** | A specialized `chart` where the X-axis is *always* `_time`. | Line or Area charts showing trends over time. |
| **`top` / `rare`** | Finds the most/least frequent values. Supports the `limit` and `showperc` clauses. | Quick frequency analysis (e.g., "Top 10 blocked IPs"). |

## 4. Geographic Mapping
Splunk can automatically map IP addresses to physical locations if piped in the correct order.

1.  **`| iplocation <ip_field>`**: This command looks up the IP in a built-in database and creates new fields: `City`, `Country`, `lat`, and `lon`.
2.  **`| geostats count by <field>`**: This takes the `lat` and `lon` generated above and aggregates the data to be rendered on a map.
    * *Map Types:* **Cluster Map** (bubbles of different sizes) and **Choropleth Map** (shading based on metrics).

## 5. Key Concept: Single Series vs. Multi-Series
* **Single Series:** Uses one field to group data (e.g., `chart count by status`). The first column becomes the X-axis.
* **Multi-Series:** Uses two fields (e.g., `chart count over status by host`). Splunk limits the number of columns rendered by default to 10 to prevent browser crashes.

## 🧠 Key Takeaway
The difference between `stats`, `chart`, and `timechart` dictates the visualization. If I want to see a trend line of failed logins over the last 24 hours, `timechart` is mandatory because it natively buckets the data by time (`span`).