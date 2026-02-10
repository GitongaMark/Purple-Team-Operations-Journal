# 🧠 Daily Drill: PowerShell Fundamentals (Microsoft Learn)
**Date:** 2026-02-03

**Source:** Microsoft Learn Module

**Topic:** Core Concepts, Navigation, and Discovery

## 1. Core Concepts
* **What is PowerShell?** An automation solution consisting of a command-line shell, a scripting language, and a configuration framework.
* **Architecture:**
    * **Verb-Noun Syntax:** All cmdlets follow a consistent naming convention (e.g., `Get-Service`, `Stop-Process`).
    * **Versions:**
        * **Windows PowerShell:** Built on .NET Framework (Legacy/Standard).
        * **PowerShell (Core):** Cross-platform, built on .NET Core.
        * *Command:* `$PSVersionTable` checks the current version.
* **Hosts/Editors:**
    * **Console:** Standard command-line interface. customizable (fonts, colors).
    * **ISE (Integrated Scripting Environment):** Graphical host with script editor and debugger (Legacy).
    * **VS Code:** The modern, recommended editor for PowerShell scripting.

## 2. Navigation & File Management (Aliases)
PowerShell uses **Aliases** to map familiar DOS/Linux commands to PowerShell cmdlets.

| Alias / Command | Actual Cmdlet | Description |
| :--- | :--- | :--- |
| `dir` / `ls` | **`Get-ChildItem`** | Lists files and folders. |
| `cd` | **`Set-Location`** | Changes the current directory. |
| `mkdir` | **`New-Item`** | Creates a new directory (ItemType Directory). |
| `del` | **`Remove-Item`** | Deletes files or folders. |
| `cat` | **`Get-Content`** | Reads the content of a file. |

**Discovery Command:**
* `Get-Alias dir` -> Reveals that `dir` runs `Get-ChildItem`.

## 3. The Discovery System (How to find help)
The most important skill in PowerShell is knowing how to find commands without memorizing them.

### Finding Commands
* **`Get-Command`:** Lists all available commands.
    * *Example:* `Get-Command –Noun event* –Verb Get` (Finds commands that *get* events).
* **`Get-Verb`:** Lists approved verbs (Standardizes actions).
* **`Get-Module -ListAvailable`:** Lists installed modules (bundles of cmdlets).

### Getting Help
* **`Get-Help <cmdlet>`:** Shows syntax and description.
    * *Examples:* `Get-Help Get-ChildItem -Examples` (Shows practical use cases).
    * *Online:* `Get-Help <cmdlet> -Online` (Opens browser documentation).
    * *Windowed:* `Get-Help about_common_parameters -ShowWindow` (Opens help in a popup).
* **`Update-Help`:** Downloads the latest help files (Modern PowerShell doesn't ship with them by default).
* **`Show-Command`:** Opens a GUI window to help build a command visually.

## 4. Pipeline & Filtering
PowerShell passes **objects**, not text, through the pipeline (`|`).

* **`Sort-Object`:** `dir | sort LastAccessTime` (Sorts files by date).
* **`Measure-Object`:** Counts objects or lines.
* **`Select-Object`:** Picks specific properties or rows (e.g., `-First 1`).

## 5. Security
* **`Get-ExecutionPolicy`:** Checks current restriction level.
* **`Set-ExecutionPolicy`:** Changes the policy (e.g., to allow scripts to run).

## 🧠 Key Takeaway
PowerShell is "Discoverable." I don't need to memorize every command; I just need to understand the **Verb-Noun** structure and how to use `Get-Command` and `Get-Help` to find what I need.