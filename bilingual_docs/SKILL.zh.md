---
name: Bilingual Documentation Manager
description: 确保所有 markdown 文档由 Agent 维护中英文双语版本。
---

# 双语文档管理器

## 目的
此技能确保项目中的每个 Markdown 文件 (`.md`) 都有一个对应的中文版本 (`.zh.md`)，并且它们保持同步。这允许用户用母语阅读文档，同时保留标准英文文档以确保技术正确性和兼容性。

## Agent 指令

1.  **创建规则**：每当你创建一个新的 markdown 文件（例如 `PLAN.md`）时，你必须立即使用你自己的翻译能力创建其对应的中文版本（例如 `PLAN.zh.md`）。
    - 中文版本的内容应该是英文版本的直接翻译。
    - 严格保留所有代码块、命令和文件路径与英文版本一致。
    - 翻译标题、描述、列表和注释。

2.  **更新规则**：当你显著编辑现有的 markdown 文件时，你必须使用你自己的翻译能力更新对应的另一种语言的文件。
    - 如果你编辑 `README.md`，更新 `README.zh.md`。
    - 如果你编辑 `README.zh.md`（例如基于用户反馈），更新 `README.md`。
    - 如果你不确定特定技术术语的翻译，请保留英文术语或使用公认的中文技术对应词。

3.  **命名约定**：
    - 英文文件：`filename.md`
    - 中文文件：`filename.zh.md`
    - *例外*：如果文件是 `README.md`，则对应文件是 `README.zh.md`。
    - **关键**：任务工件（`implementation_plan.md`, `task.md`, `walkthrough.md`）必须有对应的 `*.zh.md` 副本。英文版本供 Agent 执行/更新，中文版本供用户审查。

4.  **内容结构**：
    - 保持两个文件之间的结构（标题、嵌套）相同。这使得比较和同步更容易。

5.  **批量处理**：
    - 如果你注意到多个文件不同步（例如，在安装了一组新技能后），你必须主动逐个创建丢失的中文翻译。
    - **不要**要求用户运行脚本。**你**就是翻译者。

## 示例
**输入 (英文)**:
```markdown
# Installation
Run the following command:
`npm install`
```

**输出 (中文)**:
```markdown
# 安装
运行以下命令：
`npm install`
```
