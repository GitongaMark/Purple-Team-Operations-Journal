# 🐧 Daily Drill: Advanced Bash & Text Processing
**Date:** 2026-02-20
**Source:** cmdchallenge.com
**Topic:** Stream Editing, Algorithms, and Process Substitution

## 1. Executive Summary
Today, I tackled advanced challenges on cmdchallenge.com. The focus shifted from simple navigation to complex text manipulation. I learned how to deduplicate lines while preserving order, perform mathematical calculations (finding primes) using `awk`, and compare datasets using `comm` with process substitution.

## 2. File & Directory Logic
I refined my use of `find` to handle specific formatting requirements and bulk renaming.

| Challenge | Command Used | My Notes |
| :--- | :--- | :--- |
| **Recursive Make** | `mkdir -p tmp/files` | The `-p` flag creates parents as needed. |
| **Strip Paths** | `find . -type f -printf "%f\n"` | `%f` prints only the filename, removing the leading `./path/`. |
| **Bulk Rename** | `find . -type f -exec rename 's/\.[^.]+$//' {} +` | Used regex inside `rename` to strip extensions. |
| **Complex Find** | `find . -type f -name '[0-9]*' -printf "%f\n"` | Regex `[0-9]*` matches files starting with a digit. |

## 3. Stream Editing (`sed`, `tr`, `tac`)
I practiced manipulating data streams on the fly.

* **Replacing Characters:**
    * *Command:* `find ... | tr ' ' '.'`
    * *Logic:* `tr` (translate) is the fastest way to swap single characters (spaces to dots).
* **Specific Line Extraction:**
    * *Command:* `sed -n '25p' faces.txt`
    * *Logic:* `-n` suppresses output, and `25p` prints only the 25th line.
* **Reversing Output:**
    * *Command:* `tac reverse-me.txt`
    * *Logic:* `tac` is literally `cat` backwards.

## 4. Advanced Logic (`awk` & `comm`)
This was the most technical part of the drill. I used `awk` as a scripting language and `comm` for set analysis.

### Deduplication (Preserving Order)
* **Problem:** Remove duplicates from `faces.txt` *without* sorting (which ruins the order).
* **Solution:** `awk '!seen[$0]++' faces.txt`
* **How it works:** This is a famous idiom. It creates an array `seen`. If the line `$0` hasn't been seen (value 0), it prints. If it *has* been seen (value > 0), it skips.

### Math in the Shell (Prime Numbers)
I wrote a full function inside the command line to calculate primes.
```bash
awk '
function isprime(n) {
  if (n < 2) return 0
  for (i = 2; i * i <= n; i++)
    if (n % i == 0) return 0
  return 1
}
isprime($1) { print $1 }
' random-numbers.txt | sort -u | wc -l
```
---

# Set Intersection (Common IPs)

I needed to find IP addresses that existed in both `access.log.1` and `access.log.2`.

## Command

```bash
comm -12 <(awk '{print $1}' access.log.1 | sort) <(awk '{print $1}' access.log.2 | sort)
```

---

# The Breakthrough

`comm` requires sorted files. Instead of creating temporary files, I used **Process Substitution** `<(...)` to feed the sorted outputs of `awk` directly into `comm`.

- `-1`: Suppress lines unique to file 1.  
- `-2`: Suppress lines unique to file 2.  
- `-3`: Suppress lines common to both (left implied, so only common lines remain).

---

🧠 **Key Takeaway**  
The shell is not just for running tools; it is a programming environment. **Process substitution `<(...)`** is a game-changer—it allows me to compare the outputs of two commands as if they were files, without cluttering the disk with temporary data.