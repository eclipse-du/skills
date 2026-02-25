---
name: notebooklm-manager
description: 当你需要根据用户的关注点（如“芥子智能”、“玩具”等）从 NotebookLM 工作区搜索文档、总结数据或提取知识时，请使用此功能。
---

# NotebookLM Manager Skill

## 概述
此技能集中并自动将请求路由到特定的 NotebookLM 工作区。通过在此技能中缓存 Notebook ID，我们消除了每次搜索笔记本列表的需要，确保研究、查询和文档检索的快速、一致和可靠的定位。

## 何时使用

- 当用户询问 "芥子智能"、"sinapis"、"学件" 时 → 路由到 **SinapisAI 工作区**。
- 当用户询问 "玩具"、"老人玩具", "AI Companion" 时 → 路由到 **Toys 工作区**。
- *不要将此功能*用于一般搜索，除非用户明确要求将新类别添加到此技能的缓存中。

## 使用缓存

### 当前缓存
在与 NotebookLM MCP 交互时（`query_notebook`、`manage_source`、`manage_notebook`），直接使用以下笔记本 ID：

```yaml
workspaces:
  "SinapisAI":
    id: "f49bad64-aca7-4f29-8bbf-58195fa32451"
    name: "SinapisAI Learnware Platform Business Plan"
    keywords: ["芥子智能", "sinapis", "学件", "business plan"]
  "Toys":
    id: "2b746227-5ee0-4b27-924e-7cd87e5d0262"
    name: "The AI Companion: Smart Integration for Senior Living"
    keywords: ["玩具", "toys", "companion", "老人"]
  "ShiShanhe_Profile":
    id: "18faab83-596d-43a4-854a-53fa7d0a5167"
    name: "The Personal Profile and Aspirations of Shi Shanhe"
    keywords: ["个人简历", "石山核", "shi shanhe"]
  "XHS_Makeup":
    id: "71ab0a6d-6149-4530-9294-f09950af8710"
    name: "小红书: 雅诗兰黛腮红棒推荐"
    keywords: ["小红书", "雅诗兰黛", "腮红棒", "makeup"]
```

### 扩展缓存
如果用户要求添加新的目的地或焦点（例如，“把这个新项目加到notebooklm”），**你必须更新 `SKILL.md` 和 `SKILL.zh.md` 文件**，将新的 ID 和关键字路由规则附加到上面的 YAML 缓存中。

## 执行工作区操作

- **查询**：使用 MCP `query_notebook` 工具与上方特定的 `id` 聊天。
- **数据导出/备份**：如果需要备份整个工作区的所有来源文档，**请直接使用 MCP 工具** `manage_notebook`，并将 `action` 设置为 `"export_sources"`，同时提供绝对路径 `output_dir` (例如 `d:\du\code\knowledge\sinapisAI`)。**不要**自己编写任何额外的备份 Node 脚本。

## 服务和配置路径

Antigravity NotebookLM MCP 服务已永久部署在以下位置：
`d:\du\code\user_projects\tools\antigravity-notebooklm-mcp`

在执行相关任务或在 Agent 设置中未发现对应工具时，**请确保从该路径加载 MCP 服务**。如果遇到构建错误或者环境问题，你应该定位到该目录下执行 `npm install` 或 `npm run build`。
