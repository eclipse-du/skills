---
name: Terminal Encoding Fixer
description: Automatically fixes garbled characters in terminal output by setting global variables and encoding to force UTF-8 usage.
---

# Terminal Encoding Fixer

## Purpose
Ensures readable command-line output and eliminates garbled characters by forcing UTF-8 encoding through global environment variables and terminal configurations.

## Agent Instructions
When you encounter garbled terminal output or user feedback reporting "garbled characters" (乱码), you must execute the following operations to fix the environment.

### 1. Set PowerShell Encoding
In the Windows PowerShell environment, run the following command to force input/output to use UTF-8:

```powershell
$OutputEncoding = [System.Console]::OutputEncoding = [System.Console]::InputEncoding = [System.Text.Encoding]::UTF8
```

### 2. Change Code Page
Run the `chcp` command to change the code page to 65001 (UTF-8):

```powershell
chcp 65001
```

### 3. Set Environment Variables
To ensure compatibility between Python and other tools, set the following environment variables. You can use `run_command` to set permanent environment variables (requires user confirmation) or inject them temporarily when running commands.

Recommended variables:
*   `PYTHONIOENCODING=utf-8`
*   `LANG=en_US.UTF-8`

#### Permanent Setting (Recommended)
If this is a frequent issue, it is recommended to run the following command to permanently write the variables to the user environment:

```powershell
[System.Environment]::SetEnvironmentVariable('PYTHONIOENCODING', 'utf-8', 'User')
```

## Usage Example
Before running a script that may produce garbled characters:

```powershell
$env:PYTHONIOENCODING="utf-8"; chcp 65001; python my_script.py
```
