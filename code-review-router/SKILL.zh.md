---
name: code-review-router (代码审查路由器)
description: 根据技术栈、复杂度及变更特征，智能地将代码审查请求路由至 Gemini CLI 或 Codex CLI。当你需要对当前变更进行自动代码审查时使用。
---

# 代码审查路由器 (Code Review Router)

根据变更特征，将代码审查路由至最佳的 CLI 工具（Gemini 或 Codex）。

## 何时**不**使用此技能

- 非代码类审查（文档校对、文章编辑）
- 审查你无法控制的外部/第三方代码
- 生成提交信息（请使用专门的 commit skill）
- 当你需要指定的审查者时（直接使用该 CLI）

## 步骤 0: 环境检查

验证当前是否在 git 仓库中：

```bash
git rev-parse --git-dir 2>/dev/null || echo "NOT_A_GIT_REPO"
```

**如果不是 git 仓库：** 停止并通知用户：“此目录不是 git 仓库。请使用 `git init` 初始化或导航至仓库目录。”

## 步骤 1: 前置条件检查

验证两个 CLI 是否可用：

```bash
# 检查 Gemini CLI
which gemini || echo "GEMINI_NOT_FOUND"

# 检查 Codex CLI
which codex || echo "CODEX_NOT_FOUND"
```

**如果两个 CLI 都未找到：** 停止并通知用户他们需要安装至少一个：
- Gemini: 查看 Google Gemini CLI 安装文档
- Codex: 查看 OpenAI Codex CLI 安装文档

**如果只有一个 CLI 可用：** 使用该 CLI（无需路由）。

**如果两个都可用：** 继续进行路由分析。

## 步骤 2: 分析 Git Diff

运行以下命令收集 diff 统计信息：

```bash
# 获取 diff 统计 (已暂存 + 未暂存)
git --no-pager diff --stat HEAD 2>/dev/null || git --no-pager diff --stat

# 获取完整 diff 用于模式分析
git --no-pager diff HEAD 2>/dev/null || git --no-pager diff

# 计算变更文件数
git --no-pager diff --name-only HEAD 2>/dev/null | wc -l

# 计算总变更行数
git --no-pager diff --numstat HEAD 2>/dev/null | awk '{added+=$1; removed+=$2} END {print added+removed}'
```

**如果未检测到变更：** 报告“无需审查 - 未发现未提交的变更。”并停止。

## 步骤 3: 计算复杂度评分

初始化 `complexity_score = 0` (复杂度评分)，然后根据情况加分：

| 条件 | 分数 | 检测方法 |
|-----------|--------|------------------|
| 变更文件数 > 10 | +2 | `git diff --name-only \| wc -l` |
| 变更文件数 > 20 | +3 | (额外加分, 总计 +5) |
| 变更行数 > 300 | +2 | `git diff --numstat` 总和 |
| 变更行数 > 500 | +3 | (额外加分, 总计 +5) |
| 涉及多个目录 | +1 | 统计变更文件中的唯一目录数 |
| 包含测试文件 | +1 | 文件匹配 `*test*`, `*spec*` |
| 配置文件变更 | +1 | 文件: `*.config.*`, `*.json`, `*.yaml`, `*.yml`, `*.toml` |
| 数据库/Schema 变更 | +2 | 文件: `*migration*`, `*schema*`, `*.sql`, `prisma/*` |
| API 路由变更 | +2 | 文件在 `api/`, `routes/` 中, 或包含 `endpoint`, `handler` |
| 服务层变更 | +2 | 文件在 `services/` 中, 或匹配 `*service*`, `*provider*` |

## 步骤 4: 检测语言与框架

分析文件扩展名和内容模式：

### 主要语言检测
```
.ts, .tsx     → TypeScript
.js, .jsx     → JavaScript
.py           → Python
.go           → Go
.rs           → Rust
.java         → Java
.rb           → Ruby
.php          → PHP
.cs           → C#
.swift        → Swift
.kt           → Kotlin
```

### 框架检测 (检查导入/文件模式)
```
React/Next.js    → "import React", "from 'react'", "next.config", pages/, app/
Vue              → ".vue" 文件, "import Vue", "from 'vue'"
Angular          → "angular.json", "@angular/core"
Django           → "django", "models.py", "views.py", "urls.py"
FastAPI          → "from fastapi", "FastAPI("
Express          → "express()", "from 'express'"
NestJS           → "@nestjs/", "*.module.ts", "*.controller.ts"
Rails            → "Gemfile" 中包含 rails, app/controllers/
Spring           → "springframework", "@RestController"
```

### 安全敏感模式

通过 **文件路径** 或 **代码内容** 检测：

**文件路径：**
```
**/auth/**
**/security/**
**/*authentication*
**/*authorization*
**/middleware/auth*
```

**代码模式 (在 diff 内容中)：**
```
password\s*=
api_key\s*=
secret\s*=
Bearer\s+
JWT
\.env
credentials
private_key
access_token
```

**配置文件：**
```
.env*
*credentials*
*secrets*
*.pem
*.key
```

## 步骤 5: 应用路由决策树

**路由优先级顺序** (从上到下评估，首个匹配生效)：

### 优先级 1: 基于模式的规则 (硬性规则)

| 模式 | 路由 | 理由 |
|---------|-------|--------|
| 检测到安全敏感文件/代码 | **Codex** | 需要仔细的安全分析 |
| 文件数 > 20 或 行数 > 500 | **Codex** | 大型变更集需要彻底审查 |
| 数据库迁移或 Schema 变更 | **Codex** | 架构风险 |
| API/服务层修改 | **Codex** | 后端架构变更 |
| 变更跨越 3+ 个顶级目录 | **Codex** | 多服务影响 |
| 复杂的 TypeScript (泛型, 类型工具) | **Codex** | 类型系统复杂性 |
| 纯前端 (jsx/tsx/vue/css/html) | **Gemini** | 较简单，注重视觉的审查 |
| Python 生态 (py, Django, FastAPI) | **Gemini** |强大的 Python 支持 |
| 仅文档 (md/txt/rst) | **Gemini** | 简单的文本审查 |

### 优先级 2: 复杂度评分 (若无模式匹配)

| 分数 | 路由 | 理由 |
|-------|-------|--------|
| ≥ 6 | **Codex** | 高复杂度值得更深度的分析 |
| < 6 | **Gemini** | 中等复杂度，优先考虑速度 |

### 优先级 3: 默认

→ **Gemini** (对于不明确的情况，反馈循环更快)

## 步骤 6: 执行审查

### 解释路由决策

执行前，输出：

```
## Code Review Routing (代码审查路由)

**Changes detected (检测到的变更):**
- Files: [X] files changed
- Lines: [Y] lines modified
- Primary language: [language]
- Framework: [framework or "none detected"]

**Complexity score (复杂度评分):** [N]/10
- [List contributing factors]

**Routing decision (路由决策):** [Gemini/Codex]
- Reason: [primary reason for choice]

**Executing review (正在执行审查)...**
```

### CLI 命令

> **注意：** Gemini 接收通过 stdin (管道) 传入的 diff，而 Codex 有一个专门的 `review` 子命令直接读取 git 上下文。如果调试，请在运行 Gemini 前检查 `git diff HEAD` 是否有输出。

**对于 Gemini：**
```bash
# 将 diff 管道传输给 Gemini 并附带审查提示词
git --no-pager diff HEAD | gemini -p "Review this code diff for: 1) Code quality issues, 2) Best practices violations, 3) Potential bugs, 4) Security concerns, 5) Performance issues. Provide specific, actionable feedback."
```

**对于 Codex：**
```bash
# 使用专门的 'review' 子命令进行非交互式代码审查
# 注意: --uncommitted 和 [PROMPT] 是互斥的
codex review --uncommitted
```

## 步骤 7: 处理失败与回退

如果选定的 CLI 失败（非零退出码或错误输出）：

1. **报告失败：**
   ```
   [Primary CLI] failed: [error message]
   Attempting fallback to [other CLI]...
   ```

2. **尝试备用 CLI**

3. **如果回退也失败：**
   ```
   Both review CLIs failed.
   - Gemini error: [error]
   - Codex error: [error]

   Please check CLI installations and try manually.
   ```

## 步骤 8: 格式化输出

清晰地展示审查结果：

```
## Code Review Results (代码审查结果)

**Reviewed by:** [Gemini/Codex]
**Routing:** [brief reason]

---

[CLI output here]

---

**Review complete.** [X files, Y lines analyzed]
```

## 快速参考

| 变更类型 | 路由 | 理由 |
|-------------|-------|--------|
| React 组件样式 | Gemini | 纯前端 |
| Django 视图更新 | Gemini | Python 生态 |
| 单个 Bug 修复 < 50 行 | Gemini | 简单变更 |
| 新 API 端点 + 测试 | Codex | 架构性变更 |
| 认证系统变更 | Codex | 安全敏感 |
| 数据库迁移 | Codex | Schema 变更 |
| 多服务重构 | Codex | 高复杂度 |
| TypeScript 类型重构 | Codex | 复杂类型 |
