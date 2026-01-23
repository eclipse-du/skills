---
name: Code Directory Manager (代码目录管理器)
description: 在代码目录下创建新文件或项目时，强制执行目录结构规则。
---

# 代码目录管理器 (Code Directory Manager)

## 目的
通过根据类型将新文件和项目自动分类到特定目录中，保持工作区的整洁和有序。

## Agent 指令

**触发条件**：每当你准备在 `d:\du\code`（或工作区根目录）中创建新文件、文件夹或项目时，你**必须**查阅此技能。

### 1. 分类规则

*   **AI 生成的项目 (AI-Generated Projects)**：
    *   **定义**：任何完全或大部分由 AI Agent（你）或其他 AI 工具生成的项目、应用程序或脚本。
    *   **目标位置**：`d:\du\code\ai_generated\`
    *   *示例*：用户请求的用于数据分析的新 Python 脚本，或新的 React 应用程序。

*   **资产 / 素材 (Assets / Materials)**：
    *   **定义**：静态资源、参考图像、原始数据文件、图标或设计资产。
    *   **目标位置**：`d:\du\code\assets\`
    *   *注意*：如果 `assets` 目录不存在，请创建它。

*   **临时文件 (Temporary Files)**：
    *   **定义**：草稿、日志、快速测试脚本或不打算永久保存的中间文件。
    *   **目标位置**：`d:\du\code\tmp\`
    *   *注意*：如果 `tmp` 目录不存在，请创建它。

*   **用户项目 / 手工工作 (User Projects / Manual Work)**：
    *   **定义**：明确标识为用户主导或遗留代码的项目。
    *   **目标位置**：`d:\du\code\user_projects\`

### 2. 执行协议

1.  确定文件/项目的**类别**。
2.  **验证**目标目录是否存在。如果不存在，创建它。
3.  在正确的子目录中**创建**文件/项目。
4.  通知用户位置：“为了保持您的工作区整洁，我已将项目创建在 `ai_generated/...`。”

### 3. 强制的文件系统结构

```text
d:\du\code\
├── ai_generated/       # AI Created Projects (AI 创建的项目)
├── user_projects/      # User Created Projects (用户创建的项目)
├── assets/             # Materials/Resources (素材/资源)
├── tmp/                # Temporary files (临时文件)
└── .agent/             # Agent configuration (Agent 配置)
```
