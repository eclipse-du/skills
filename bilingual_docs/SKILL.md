---
name: Bilingual Documentation Manager
description: Ensures all markdown documentation is maintained in both English and Chinese by the Agent.
---

# Bilingual Documentation Manager

## Purpose
This skill ensures that every Markdown file (`.md`) in the project has a corresponding Chinese version (`.zh.md`) and that they are kept in sync. This allows the user to read documentation in their native language while maintaining standard English documentation for technical correctness and compatibility.

## Instructions for the Agent

1.  **Creation Rule**: Whenever you create a new markdown file (e.g., `PLAN.md`), you MUST immediately create its Chinese counterpart (e.g., `PLAN.zh.md`) using your own translation capabilities.
    - The content of the Chinese version should be a direct translation of the English version.
    - Preserve all code blocks, commands, and file paths exactly as they are in the English version.
    - Translate headers, descriptions, lists, and comments.

2.  **Update Rule**: When you significantly edit an existing markdown file, you MUST update the corresponding other-language file using your own translation capabilities.
    - If you edit `README.md`, update `README.zh.md`.
    - If you edit `README.zh.md` (e.g. based on user feedback), update `README.md`.
    - If you are unsure about the translation of a specific technical term, stick to the English term or the generally accepted Chinese technical translation.

3.  **Naming Convention**:
    - English File: `filename.md`
    - Chinese File: `filename.zh.md`
    - *Exception*: If the file is `README.md`, the pair is `README.zh.md`.
    - **Critical**: Task artifacts (`implementation_plan.md`, `task.md`, `walkthrough.md`) MUST have a corresponding `*.zh.md` counterpart. The English version is for the Agent to execute/update, the Chinese version is for the User to review.

4.  **Content Structure**:
    - Keep the structure (headers, nesting) identical between both files. This makes comparison and syncing easier.

5.  **Batch Processing**:
    - If you notice multiple files are out of sync (e.g., after installing a new set of skills), you must proactively create the missing Chinese translations one by one.
    - Do NOT ask the user to run scripts. YOU are the translator.

## Example
**Input (English)**:
```markdown
# Installation
Run the following command:
`npm install`
```

**Output (Chinese)**:
```markdown
# 安装
运行以下命令：
`npm install`
```
