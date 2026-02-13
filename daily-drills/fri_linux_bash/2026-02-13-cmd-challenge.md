# 🐧 Daily Drill: Command Line Challenge

**Date:** 2026-02-13
**Source:** cmdchallenge.com
**Topic:** Bash Text Processing & File Manipulation

## 1. Directory & File Management
| Command | Purpose / Concept |
| :--- | :--- |
| `mkdir -p tmp/files` | Creates a directory and any necessary parent directories (`-p`). |
| `find . -type f -name "*.doc" -delete` | Recursively locates files by extension and safely deletes them. |
| `ls access.log*` | Lists files matching a specific prefix pattern in the current directory. |
| `find . -maxdepth 1 -type f \| wc -l` | Counts the number of files strictly in the current directory (no recursion). |

## 2. Text Search & Extraction (`grep` & `find`)
| Command | Purpose / Concept |
| :--- | :--- |
| `grep "GET" access.log` | Standard text filtering. Prints lines containing the exact string. |
| `grep -l "500" *` | The `-l` flag lists *only the filenames* containing the match, rather than the matched text. |
| `grep -h "500" $(find . -type f -name "access.log*")` | Searches recursively using command substitution. The `-h` flag hides filenames in the final output. |
| `grep -c "GET" access.log` | The `-c` flag counts the total number of matching lines instead of printing them. |
| `find . -type f -name "access.log*" -exec grep -hEo '([0-9]{1,3}\.){3}[0-9]{1,3}' {} +` | Advanced extraction. Uses `find` to pass files to `grep`, which then uses Regex (`-E`) to extract (`-o`) IP addresses. |

## 3. Data Manipulation (`sed`, `awk`, `tr`, `sort`)
| Command | Purpose / Concept |
| :--- | :--- |
| `sort access.log` | Alphabetically sorts the contents of a file. |
| `tr ';' '\n' < split-me.txt` | Translates or replaces specific characters (converting semicolons into new lines). |
| `seq -s " " 1 100` | Generates a sequence of numbers, using a space (`-s " "`) as the separator. |
| `find . -type f -name "*.txt" -exec sed -i 's/challenges are difficult//g' {} +` | Uses `sed` to perform an in-place (`-i`) search and replace operation across multiple files simultaneously. |
| `awk '{sum += $1} END {print sum}' sum-me.txt` | Uses `awk` for mathematical operations. It iterates through the first column (`$1`), adds it to a `sum` variable, and prints the total at the end. |

## 🧠 Key Takeaway
Piping commands together is the true power of Bash. Tools like `find` and `grep` are excellent for locating data, but mastering `sed`, `awk`, and `tr` allows for programmatic manipulation of that data directly from the terminal without needing a heavy scripting language like Python.