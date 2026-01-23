---
name: Terminal Encoding Fixer
description: 自动修复终端输出中的乱码问题，通过设置全局变量和编码强制使用 UTF-8。
---

# 终端编码修复器 (Terminal Encoding Fixer)

## 目的
通过设置全局环境变量和终端配置强制使用 UTF-8 编码，确保命令行输出可读，消除乱码。

## Agent 指令
当你遇到终端输出乱码，或用户反馈出现“乱码”时，你必须执行以下操作来修复环境。

### 1. 设置 PowerShell 编码
在 Windows PowerShell 环境中，运行以下命令以强制输入输出使用 UTF-8：

```powershell
$OutputEncoding = [System.Console]::OutputEncoding = [System.Console]::InputEncoding = [System.Text.Encoding]::UTF8
```

### 2. 更改代码页 (Code Page)
运行 `chcp` 命令将代码页更改为 65001 (UTF-8)：

```powershell
chcp 65001
```

### 3. 设置环境变量
为了保证 Python 与其他工具的兼容性，设置以下环境变量。你可以使用 `run_command` 来设置永久环境变量（需要用户确认），或在运行命令时临时注入。

建议设置的变量：
*   `PYTHONIOENCODING=utf-8`
*   `LANG=en_US.UTF-8`

#### 永久设置 (推荐)
如果这是一个经常发生的问题，建议运行以下命令将变量永久写入用户环境：

```powershell
[System.Environment]::SetEnvironmentVariable('PYTHONIOENCODING', 'utf-8', 'User')
```

## 使用示例
在运行可能产生乱码的脚本前：

```powershell
$env:PYTHONIOENCODING="utf-8"; chcp 65001; python my_script.py
```
