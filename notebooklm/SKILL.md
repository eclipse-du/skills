---
name: notebooklm-manager
description: Use when you need to act on specific information, search documents, summarize data, or extract knowledge from NotebookLM workspaces based on the user's focus (e.g., "SinapisAI", "toys").
---

# NotebookLM Manager Skill

## Overview
This skill centralizes and automates routing requests to specific NotebookLM workspaces. By caching the Notebook IDs inside this skill, we eliminate the need to search the notebook list every time, ensuring fast, consistent, and reliable targeting for research, querying, and document retrieval.

## When to Use

- When the user asks about "芥子智能", "sinapis", "学件", "商业计划" → Route to **SinapisAI Workspace**.
- When the user asks about "玩具", "老人玩具", "AI Companion" → Route to **Toys Workspace**.
- *Do not use this* for general search unless the user explicitly asks to add a new category to this skill's cache.

## Working with the Cache

### Current Cache
Use these Notebook IDs directly when interacting with the NotebookLM MCP (`query_notebook`, `manage_source`, `manage_notebook`):

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

### Expanding the Cache
If the user asks to add a new destination or focus (e.g., "把这个新项目加到notebooklm"), **you must update this SKILL.md and SKILL.zh.md file** to append the new ID and keyword routing rules to the YAML cache above.

## Executing Workspace Operations

- **Querying**: Use the MCP `query_notebook` tool to chat with the specific `id` above.
- **Data Export/Backup**: If you need to back up all sources of a whole workspace, **directly use the MCP tool** `manage_notebook` with `action="export_sources"` and provide an absolute `output_dir` (e.g. `d:\du\code\knowledge\sinapisAI`). **DO NOT** write any extra standalone Node backup scripts yourself.

## Server Location & Configuration

The Antigravity NotebookLM MCP server is permanently located at:
`d:\du\code\user_projects\tools\antigravity-notebooklm-mcp`

If the NotebookLM tools are missing from your environment, or you are instructed to query notebooks but lack the capability, **you must ensure the MCP server is loaded from this path**. If there are environment issues, navigate to this directory to run `npm install` or `npm run build`.
