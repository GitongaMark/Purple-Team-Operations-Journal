# 🦠 Daily Drill: YARA Source Installation & Rule Fundamentals
**Date:** 2026-02-14  
**Source:** yara.readthedocs.io  
**Topic:** Compiling from Source & Static Analysis Syntax  

---

## 1. Executive Summary

Upgraded the malware analysis environment by compiling **YARA (v4.5.5)** directly from the source tarball on Kali Linux. This ensures access to the latest engine updates rather than relying on repository packages.

Additionally, reviewed the core anatomical requirements of YARA rules, specifically:

- String definitions (Hex vs. Text)  
- Boolean conditions  
- Reserved keywords  

---

## 2. Compiling YARA from Source

Unlike standard package managers (`apt`), compiling from source requires build tools and a specific sequence of commands to configure the software for the exact architecture of the host machine.

### 🔧 Prerequisites

Installed the necessary C compilers and build utilities:

```bash
sudo apt-get install automake libtool make gcc pkg-config
```
## 🏗️ The Build Process

Extracted the release and built the binary:

```bash
tar -zxf yara-4.5.5.tar.gz
cd yara-4.5.5
./bootstrap.sh
./configure
make
sudo make install
```
**Verification:**  
Ran `make check` to execute the built-in test suites and confirm the compilation was successful without architecture errors.

---

## 3. The Smoke Test

To verify the engine works, I created and executed a "dummy" rule against itself.

### Command:

```bash
echo "rule dummy { condition: true }" > my_first_rule
yara my_first_rule my_first_rule
```
### Result:

```bash
dummy my_first_rule
```
This confirms the engine successfully parsed the rule and scanned the target file.

---

## 4. YARA Rule Anatomy

A standard YARA rule acts as a boolean logic gate. It evaluates to:

- **true** → Detection  
- **false** → Clean  

---

### 🧱 Structure

Rules are composed of two main sections:

- `strings:` (Optional but common) — The definition block  
- `condition:` (Required) — The logic block  

---

### 💻 Code Snippet

```yara
rule ExampleRule {    
    strings:        
        $my_text_string = "text here"            // Text strings enclosed in double quotes     
        $my_hex_string  = { E2 34 A1 C8 23 FB }  // Hex enclosed in curly brackets
    condition:        
        $my_text_string or $my_hex_string
}
```
## 5. String Rules

### 📄 Text Strings

- Formatted like C-language strings  
- Example: `"value"`

### 🔢 Hex Strings

- Formatted with spaces and curly braces  
- Example: `{ AA BB CC }`  
- Decimal numbers are strictly prohibited  

### 🏷️ Identifiers

- Must start with `$`  
- Followed by alphanumeric characters or underscores  
- Example: `$malware_sig_1`

---

## 6. Reserved Keywords

YARA reserves specific words for its logic engine that **cannot** be used as variable names (identifiers).

Key operational words include:

```text
all, and, any, ascii, at, base64, base64wide, condition, contains, 
endswith, entrypoint, false, filesize, for, fullword, global, import, 
icontains, iendswith, iequals, in, include, int16, int16be, int32, 
int32be, int8, int8be, istartswith, matches, meta, nocase, none, not, of,
or, private, rule, startswith, strings, them, true, uint16, uint16be, 
uint32, uint32be, uint8, uint8be, wide, xor, defined

```
## 🧠 Key Takeaway

YARA rule logic operates exactly like a script:

- The **`strings` section** defines the variables.  
- The **`condition` section** acts as an `if` statement.  
- The rule evaluates those variables against the target file's memory or disk footprint.