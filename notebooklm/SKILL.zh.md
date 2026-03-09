---
name: notebooklm-manager
description: 当你需要根据用户的关注点（如"芥子智能"、"玩具"等）从 NotebookLM 工作区搜索文档、总结数据或提取知识时，请使用此功能。
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
在查询或管理来源时直接使用这些笔记本 ID：

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
如果用户要求添加新的目的地或焦点（例如，"把这个新项目加到notebooklm"），**你必须更新 `SKILL.md` 和 `SKILL.zh.md` 文件**，将新的 ID 和关键字路由规则附加到上面的 YAML 缓存中。

## 认证与 Cookie 处理

### 架构
此技能使用 **Playwright (patchright)** 驱动真实的 Chrome 浏览器来访问 NotebookLM。认证通过**两级系统**工作：

1. **Windows 主机（有界面登录）** — 用户在 Windows 上直接运行 `auth_manager.py setup`。这会启动一个真实的 Chrome 窗口，用户在其中登录 Google。会话状态（Cookie + localStorage）保存到 `data/browser_state/state.json`。

2. **Docker 容器（无头查询）** — `myllm` 容器从共享卷中读取保存的 `state.json`，并将 Cookie 注入无头 Chrome 会话以执行查询。

> **关键认知**: Google 会严格检测并阻止无头浏览器登录。你**不能**在 Docker 容器内进行初始认证。必须在 Windows 主机上使用真实的可见浏览器窗口完成。

### 首次设置（或会话过期时）

在 **Windows 主机**上运行（不要在 Docker 内运行）：
```bash
python scripts/auth_manager.py setup
```
- 会打开 Chrome 浏览器窗口，用户登录 Google 帐户。
- 看到 NotebookLM 主页后，脚本自动保存状态。
- `state.json` 文件保存在共享卷上的 `data/browser_state/state.json`。

> **重要提示**：如果此命令出现 `[WinError 2]` 错误，请直接运行 `auth_manager.py` 而不是通过 `run.py`。`run.py` 包装器会尝试创建虚拟环境，可能在 Windows 上失败。

### Cookie 持久化工作原理

- `auth_manager.py setup` 使用**普通浏览器上下文**（非持久化）以避免 Chromium 默认的第三方 Cookie 阻止。
- `config.py` 中包含 Chrome 启动参数 `--disable-features=ThirdPartyCookieBlocking,TrackingProtection3pcd` 以确保 Cookie 正常工作。
- 保存的 `state.json` 包含 Playwright 格式的 Cookie 和 localStorage 数据。
- 从容器查询时，`browser_utils.py` 通过 `context.add_cookies()` 注入这些 Cookie。
- 会话通常可以持续 **7 天以上**，之后才需要重新认证。

### 检查认证状态
```bash
python scripts/auth_manager.py status
```

### 重新认证（会话过期时）
```bash
python scripts/auth_manager.py clear
python scripts/auth_manager.py setup
```

### Cookie 问题排查

| 问题 | 解决方案 |
|---|---|
| 登录时出现 "Cookie settings" 错误 | 先运行 `auth_manager.py clear`，然后重新 `setup` |
| 查询时被重定向到 Google 登录页 | 会话已过期 — 在 Windows 主机上重新运行 `setup` |
| Windows 上出现 `[WinError 2]` | 直接运行 `auth_manager.py`，不要通过 `run.py` |
| 缺少 patchright/chromium | `pip install patchright && patchright install chromium` |

## 执行工作区操作

### 运行环境
- **Windows 主机**：仅用于认证（`auth_manager.py setup`）。
- **Docker 容器 (`myllm`)**：用于所有查询和操作。代码挂载在 `/home/gpu/py/.agent/skills/notebooklm/`。

### 1. 查询工作区（Docker 容器内）
```bash
docker exec -w /home/gpu/py/.agent/skills/notebooklm/scripts myllm \
  python3 ask_question.py --question "你的问题" --notebook-id sinapisai
```
也可以直接使用 YAML 缓存中的 UUID：
```bash
docker exec -w /home/gpu/py/.agent/skills/notebooklm/scripts myllm \
  python3 ask_question.py --question "你的问题" \
  --notebook-id f49bad64-aca7-4f29-8bbf-58195fa32451
```

### 2. 管理库
```bash
docker exec -w /home/gpu/py/.agent/skills/notebooklm/scripts myllm \
  python3 notebook_manager.py list
```

### 3. 添加笔记本
```bash
docker exec -w /home/gpu/py/.agent/skills/notebooklm/scripts myllm \
  python3 notebook_manager.py add \
  --url "https://notebooklm.google.com/notebook/YOUR-ID" \
  --name "名称" --description "描述" --topics "主题1,主题2"
```
