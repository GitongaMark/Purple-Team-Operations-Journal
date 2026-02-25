# 🟠 Daily Drill: Splunk Search Under the Hood
**Date:** 2026-02-25
**Source:** education.splunk.com
**Topic:** Search Job Inspector, Pipeline Architecture, and Optimization
**Author:** Mark Gitonga

## 1. Executive Summary
Today, I looked behind the curtain of the Splunk search engine. Instead of just writing SPL, I learned how Splunk tokenizes data, processes commands across its architecture, and how to measure the exact performance of my queries using the Search Job Inspector.

## 2. The Search Job Inspector
I learned how to dissect a search's execution time to find performance bottlenecks. By opening the Job Inspector, I can see exactly where the CPU spent its time:

| Component | What it measures (My Notes) |
| :--- | :--- |
| `command.search.index` | Time spent scanning the actual index files on disk (the fastest part if filtered right). |
| `command.search.filter` | Time spent filtering the results based on my SPL logic. |
| `search.rawdata` | Time spent pulling the raw text of the events from the disk. |
| `search.kv` | Time spent extracting Key-Value pairs (Search-time field extraction). *High times here mean I should optimize my regex or use `fields` earlier.* |
| `search.lookups` | Time spent executing CSV or KV Store lookups. |

## 3. Splunk Architecture: Command Types
This was the most critical concept for query optimization. Splunk executes commands differently depending on their type.

* **Streaming Commands:** These operate on each event as it arrives. They execute in parallel across the **Indexers**. (e.g., `search`, `eval`, `rex`, `fields`). *Goal: Push these as early in the query as possible.*
* **Non-Streaming Commands:** These require the entire dataset to be gathered before they can execute. They force the data to be sent to the **Search Head** for processing. (e.g., `stats`, `sort`, `dedup`). Once a non-streaming command is used, all subsequent commands happen on the Search Head.

## 4. Data Parsing (Breakers & Segmentation)
I explored how Splunk breaks raw log lines into searchable "tokens."
* Splunk uses **Major Breakers** (spaces, tabs, newlines) and **Minor Breakers** (commas, equals signs, colons) to segment data.
* Understanding this helps me realize why searching for an exact string or an IP address is faster than using leading wildcards (`*text`), which forces Splunk to scan inside the tokens.

## 5. SPL Commenting & Troubleshooting
I documented the native tools used for keeping complex SPL readable and debugging data.

* **SPL Comments:** I can use three backticks ` ``` ` to comment out blocks of code or add notes directly in the search bar. Example:
    ```splunk
    index=security 
    ``` Filter out the noisy scanner IP ```
    | search NOT src_ip="10.0.0.5"
    ```
* **`| fieldsummary`**: A powerful command for troubleshooting. It generates a statistical profile of all fields in my search results (showing max, min, mean, and distinct count).
* **Informational Functions:** Used inside `eval` statements to check the data type of a field before manipulating it (e.g., `isnum()`, `isstr()`, `isnull()`).

## 🧠 Key Takeaway
The order of commands in Splunk matters immensely. To optimize performance, I must filter early (`search`), keep only what I need (`fields`), and delay non-streaming commands (`stats`, `sort`) until the very end of the pipeline to avoid choking the Search Head.