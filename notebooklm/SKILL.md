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
Use these Notebook IDs directly when querying or managing sources:

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

## Authentication & Cookie Handling

### Architecture
The skill uses **Playwright (patchright)** to drive a real Chrome browser for accessing NotebookLM. Authentication works through a **two-tier system**:

1. **Windows Host (Headed Login)** — The user runs `auth_manager.py setup` directly on Windows. This launches a real Chrome window where the user logs in to Google. The session state (cookies + localStorage) is saved to `data/browser_state/state.json`.

2. **Docker Container (Headless Queries)** — The `myllm` container reads the saved `state.json` from the shared volume and injects the cookies into a headless Chrome session to perform queries.

> **Key Insight**: Google strictly detects and blocks headless browser logins. You **cannot** perform the initial authentication inside a Docker container. It MUST be done on the Windows host with a real visible browser window.

### First-Time Setup (or Session Expired)

Run this on the **Windows host** (NOT inside Docker):
```bash
python scripts/auth_manager.py setup
```
- A Chrome window will open. The user logs in to their Google Account.
- After seeing the NotebookLM home page, the script auto-saves the state.
- The `state.json` file is saved to `data/browser_state/state.json` on the shared volume.

> **IMPORTANT**: If this command fails with `[WinError 2]`, run `auth_manager.py` directly instead of through `run.py`. The `run.py` wrapper tries to create a virtual environment which may fail on Windows.

### How Cookie Persistence Works

- `auth_manager.py setup` uses a **regular browser context** (not persistent) to avoid Chromium's default third-party cookie blocking.
- The `config.py` includes Chrome flags `--disable-features=ThirdPartyCookieBlocking,TrackingProtection3pcd` to ensure cookies work.
- The saved `state.json` contains Playwright-format cookies and localStorage data.
- When querying from the container, `browser_utils.py` injects these cookies via `context.add_cookies()`.
- Sessions typically last **7+ days** before needing re-authentication.

### Checking Auth Status
```bash
python scripts/auth_manager.py status
```

### Re-Authentication (if session expired)
```bash
python scripts/auth_manager.py clear
python scripts/auth_manager.py setup
```

### Troubleshooting Cookie Issues

| Problem | Solution |
|---|---|
| "Cookie settings" error during login | Run `auth_manager.py clear` first, then `setup` again |
| Redirected to Google Sign-in when querying | Session expired — re-run `setup` on Windows host |
| `[WinError 2]` on Windows | Run `auth_manager.py` directly, not through `run.py` |
| Missing patchright/chromium | `pip install patchright && patchright install chromium` |

## Executing Workspace Operations

### Environment
- **Windows Host**: For authentication only (`auth_manager.py setup`).
- **Docker Container (`myllm`)**: For all queries and operations. The code is mounted at `/home/gpu/py/.agent/skills/notebooklm/`.

### 1. Querying a Workspace (inside Docker)
```bash
docker exec -w /home/gpu/py/.agent/skills/notebooklm/scripts myllm \
  python3 ask_question.py --question "Your question here" --notebook-id sinapisai
```
Or using the YAML notebook UUID directly:
```bash
docker exec -w /home/gpu/py/.agent/skills/notebooklm/scripts myllm \
  python3 ask_question.py --question "Your question here" \
  --notebook-id f49bad64-aca7-4f29-8bbf-58195fa32451
```

### 2. Managing the Library
```bash
docker exec -w /home/gpu/py/.agent/skills/notebooklm/scripts myllm \
  python3 notebook_manager.py list
```

### 3. Adding a Notebook
```bash
docker exec -w /home/gpu/py/.agent/skills/notebooklm/scripts myllm \
  python3 notebook_manager.py add \
  --url "https://notebooklm.google.com/notebook/YOUR-ID" \
  --name "Name" --description "Description" --topics "topic1,topic2"
```
