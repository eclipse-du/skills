---
name: planning-with-files
version: "2.4.1"
description: 实现 Manus 风格的基于文件的复杂任务规划。创建 task_plan.md, findings.md, 和 progress.md。适用于开始复杂的多步骤任务、研究项目或任何需要超过 5 次工具调用的任务。现在支持使用 /clear 后自动恢复会话。
user-invocable: true
allowed-tools:
  - Read
  - Write
  - Edit
  - Bash
  - Glob
  - Grep
  - WebFetch
  - WebSearch
hooks:
  PreToolUse:
    - matcher: "Write|Edit|Bash|Read|Glob|Grep"
      hooks:
        - type: command
          command: "cat task_plan.md 2>/dev/null | head -30 || true"
  PostToolUse:
    - matcher: "Write|Edit"
      hooks:
        - type: command
          command: "echo '[planning-with-files] File updated. If this completes a phase, update task_plan.md status.'"
  Stop:
    - hooks:
        - type: command
          command: |
            SCRIPT_DIR="${CLAUDE_PLUGIN_ROOT:-$HOME/.claude/plugins/planning-with-files}/scripts"
            if command -v pwsh &> /dev/null && [[ "$OSTYPE" == "msys" || "$OSTYPE" == "win32" || "$OS" == "Windows_NT" ]]; then
              pwsh -ExecutionPolicy Bypass -File "$SCRIPT_DIR/check-complete.ps1" 2>/dev/null || powershell -ExecutionPolicy Bypass -File "$SCRIPT_DIR/check-complete.ps1" 2>/dev/null || bash "$SCRIPT_DIR/check-complete.sh"
            else
              bash "$SCRIPT_DIR/check-complete.sh"
            fi
---

# 基于文件的规划 (Planning with Files)

像 Manus 一样主要工作：使用持久的 markdown 文件作为你“磁盘上的工作记忆”。

## 第一步：检查之前的会话 (v2.2.0)

**在开始工作之前**，检查是否有来自之前会话的未同步上下文：

```bash
# Linux/macOS
$(command -v python3 || command -v python) ${CLAUDE_PLUGIN_ROOT}/scripts/session-catchup.py "$(pwd)"
```

```powershell
# Windows PowerShell
& (Get-Command python -ErrorAction SilentlyContinue).Source "$env:USERPROFILE\.claude\skills\planning-with-files\scripts\session-catchup.py" (Get-Location)
```

如果同步报告显示有未同步的上下文：
1. 运行 `git diff --stat` 查看实际的代码更变
2. 读取当前的规划文件
3. 根据同步报告和 git diff 更新规划文件
4. 然后继续任务

## 重要：文件放在哪里

- **Templates (模板)** 在 `${CLAUDE_PLUGIN_ROOT}/templates/`
- **你的规划文件** 放在 **你的项目目录** 中

| 位置 | 内容 |
|----------|-----------------|
| Skill 目录 (`${CLAUDE_PLUGIN_ROOT}/`) | 模板, 脚本, 参考文档 |
| 你的项目目录 | `task_plan.md`, `findings.md`, `progress.md` |

## 快速开始

在开始任何复杂任务之前：

1. **创建 `task_plan.md`** — 参考 [templates/task_plan.md](templates/task_plan.md)
2. **创建 `findings.md`** — 参考 [templates/findings.md](templates/findings.md)
3. **创建 `progress.md`** — 参考 [templates/progress.md](templates/progress.md)
4. **决策前重新阅读计划** — 刷新注意力窗口中的目标
5. **每个阶段后更新** — 标记完成，记录错误

> **注意：** 规划文件放在你的项目根目录，而不是 skill 安装文件夹中。

## 核心模式

```
上下文窗口 (Context Window) = 内存 (RAM) (易失性，有限)
文件系统 (Filesystem) = 磁盘 (Disk) (持久性，无限)

→ 任何重要的事情都要写入磁盘。
```

## 文件用途

| 文件 | 用途 | 何时更新 |
|------|---------|----------------|
| `task_plan.md` | 阶段，进度，决策 | 每个阶段后 |
| `findings.md` | 研究，发现 | 任何发现后 |
| `progress.md` | 会话日志，测试结果 | 整个会话期间 |

## 关键规则

### 1. 首先创建计划
如果没有 `task_plan.md`，永远不要开始复杂任务。没得商量。

### 2. 双操作规则 (The 2-Action Rule)
> “每进行 2 次查看/浏览器/搜索操作后，立即将关键发现保存到文本文件中。”

这可以防止视觉/多模态信息丢失。

### 3. 决策前阅读
在做出重大决定之前，阅读计划文件。这能让目标保持在你的注意力窗口中。

### 4. 行动后更新
完成任何阶段后：
- 标记阶段状态：`in_progress` → `complete`
- 记录遇到的任何错误
- 记录创建/修改的文件

### 5. 记录所有错误
每个错误都要记录在计划文件中。这能建立知识库并防止重复。

```markdown
## Errors Encountered
| Error | Attempt | Resolution |
|-------|---------|------------|
| FileNotFoundError | 1 | Created default config |
| API timeout | 2 | Added retry logic |
```

### 6. 永不重复失败
```
if action_failed:
    next_action != same_action
```
追踪你尝试过的方法。改变方法。

## 三次机会错误协议 (The 3-Strike Error Protocol)

```
尝试 1: 诊断与修复
  → 仔细阅读错误
  → 确定根本原因
  → 应用针对性修复

尝试 2: 替代方法
  → 同样的错误？尝试不同的方法
  → 不同的工具？不同的库？
  → 永远不要重复完全相同的失败操作

尝试 3: 重新思考
  → 质疑假设
  → 搜索解决方案
  → 考虑更新计划

3 次失败后: 上报给用户
  → 解释你尝试了什么
  → 分享具体错误
  → 寻求指导
```

## 读 vs 写 决策矩阵

| 情况 | 行动 | 原因 |
|-----------|--------|--------|
| 刚写了一个文件 | **不要** 读取 | 内容仍在上下文中 |
| 查看了图片/PDF | **立即** 写入 findings | 多模态 → 在丢失前转为文本 |
| 浏览器返回数据 | 写入文件 | 截图不会持久保存 |
| 开始新阶段 | 读取 plan/findings | 如果上下文陈旧，重新定位 |
| 发生错误 | 读取相关文件 | 需要当前状态来修复 |
| 间断后恢复 | 读取所有规划文件 | 恢复状态 |

## 5 个问题重启测试

如果你能回答这些问题，你的上下文管理就很稳固：

| 问题 | 答案来源 |
|----------|---------------|
| 我在哪里？ | task_plan.md 中的当前阶段 |
| 我要去哪里？ | 剩余阶段 |
| 目标是什么？ | 计划中的目标声明 |
| 我学到了什么？ | findings.md |
| 我做了什么？ | progress.md |

## 何时使用此模式

**适用于：**
- 多步骤任务（3步以上）
- 研究任务
- 构建/创建项目
- 跨越多次工具调用的任务
- 任何需要组织的任务

**跳过：**
- 简单问题
- 单文件编辑
- 快速查找

## 模板

复制这些模板开始：

- [templates/task_plan.md](templates/task_plan.md) — 阶段追踪
- [templates/findings.md](templates/findings.md) — 研究存储
- [templates/progress.md](templates/progress.md) — 会话日志

## 脚本

自动化辅助脚本：

- `scripts/init-session.sh` — 初始化所有规划文件
- `scripts/check-complete.sh` — 验证所有阶段完成
- `scripts/session-catchup.py` — 从上一次会话恢复上下文 (v2.2.0)

## 高级主题

- **Manus 原则：** 见 [reference.md](reference.md)
- **真实示例：** 见 [examples.md](examples.md)

## 反面模式 (Anti-Patterns)

| 不要 | 应该 |
|-------|------------|
| 使用 TodoWrite 进行持久化 | 创建 task_plan.md 文件 |
| 声明一次目标后就忘记 | 决策前重新阅读计划 |
| 隐藏错误并默默重试 | 将错误记录到计划文件中 |
| 把所有东西塞进上下文 | 将大量内容存储在文件中 |
| 立即开始执行 | **首先** 创建计划文件 |
| 重复失败的动作 | 追踪尝试，改变方法 |
| 在 skill 目录创建文件 | 在**你的项目**中创建文件 |
