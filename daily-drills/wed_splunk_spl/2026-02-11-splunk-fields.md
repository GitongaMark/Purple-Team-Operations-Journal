# 🟠 Daily Drill: Splunk Fields & Knowledge Objects
**Date:** 2026-02-12
**Source:** education.splunk.com
**Topic:** Field Extraction, Manipulation, and Optimization

## 1. The Anatomy of a Field
Splunk parses data into **Key=Value** pairs.
* **Selected Fields:** Visible in the event list "below the event." By default, this includes `host`, `source`, `sourcetype`, and `time`.
* **Interesting Fields:** Fields that appear in at least **20%** of the events in your search results.
* **Symbols:**
    * **`a`**: Denotes a **String** (Text) value.
    * **`#`**: Denotes a **Numeral** (Number) value.

## 2. The Power Operators (Logic)
Understanding the difference between `!=` and `NOT` is critical for accuracy.

| Operator | Usage | Logic |
| :--- | :--- | :--- |
| **`!=`** | `status!=200` | **Field-based.** Returns events where the field `status` exists AND is not 200. **Excludes events where `status` is null.** |
| **`NOT`** | `NOT status=200` | **Boolean.** Returns events where `status` is not 200 **OR** the field `status` doesn't exist at all. |
| **`IN`** | `status IN (400, 401, 403)` | Checks if a value exists in a list. Cleaner than `status=400 OR status=401...` |

## 3. Optimizing Searches (The `fields` Command)
This command makes searches faster by keeping only what you need.
* **`fields + user`** (or just `fields user`): **Keeps** only the `user` field. Discards everything else.
* **`fields - user`**: **Removes** the `user` field from the results.
* *Performance Note:* Using `fields` early in a search (before reporting commands) speeds up processing by reducing the data load.

## 4. Extraction: Index-Time vs. Search-Time
* **Index-Time Extraction:** Happens *when data enters Splunk*.
    * **Pros:** Permanent.
    * **Cons:** Uses more disk space; cannot be changed later.
* **Search-Time Extraction:** Happens *when you run a query*.
    * **Pros:** Flexible, dynamic, no disk penalty. **Preferred method**.
    * **Cons:** Uses CPU during the search.

## 5. Creating Fields (SPL Commands)
| Command | Function | Difficulty | Use Case |
| :--- | :--- | :--- | :--- |
| **`rex`** | Regular Expression Extract | High | The "Pro" tool. Extracts data using regex patterns. Most precise. |
| **`erex`** | Example Extract | Low | The "Easy" button. You provide an example value, and Splunk writes the regex for you. |
| **`eval`** | Evaluate | Medium | Calculates new fields (e.g., `eval MB = bytes / 1024 / 1024`). |

## 🧠 Key Takeaway
If you filter with `!=`, you might accidentally hide events where the field is missing (null). If you want to see *everything* except the specific value, use `NOT field=value`.