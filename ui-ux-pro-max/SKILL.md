---
name: ui-ux-pro-max
description: UI/UX design intelligence. 50 styles, 21 palettes, 50 font pairings, 20 charts, 9 stacks.
---

# UI UX Pro Max (UI/UX 专家)

Comprehensive design guide for web and mobile applications. Contains 67 styles, 96 color palettes, 57 font pairings, 99 UX guidelines, and 25 chart types across 13 technology stacks. Searchable database with priority-based recommendations.

适用于 Web 和移动应用程序的综合设计指南。包含 67 种风格、96 种调色板、57 种字体配对、99 条 UX 准则和 13 种技术栈中的 25 种图表类型。具有基于优先级的推荐的可搜索数据库。

## Prerequisites / 先决条件

Check if Python is installed / 检查是否安装了 Python:

```bash
python3 --version || python --version
```

## How to Use This Skill / 如何使用此技能

When user requests UI/UX work (design, build, create, implement, review, fix, improve), follow this workflow:
当用户请求 UI/UX 工作（设计、构建、创建、实现、审查、修复、改进）时，请遵循此工作流程：

### Step 1: Analyze User Requirements / 第一步：分析用户需求

Extract key information from user request / 从用户请求中提取关键信息:
- **Product type**: SaaS, e-commerce, portfolio, dashboard, landing page, etc.
- **Style keywords**: minimal, playful, professional, elegant, dark mode, etc.
- **Industry**: healthcare, fintech, gaming, education, etc.
- **Stack**: React, Vue, Next.js, or default to `html-tailwind`

### Step 2: Generate Design System (REQUIRED) / 第二步：生成设计系统（必须）

**Always start with `--design-system`** to get comprehensive recommendations with reasoning:
**始终以 `--design-system` 开始**，以获得带有推理的全面建议：

```bash
python3 .agent/skills/ui-ux-pro-max/scripts/search.py "<product_type> <industry> <keywords>" --design-system [-p "Project Name"]
```

This command / 此命令将:
1. Searches 5 domains in parallel (product, style, color, landing, typography) / 并行搜索 5 个领域
2. Applies reasoning rules from `ui-reasoning.csv` to select best matches / 应用推理规则选择最佳匹配
3. Returns complete design system: pattern, style, colors, typography, effects / 返回完整设计系统
4. Includes anti-patterns to avoid / 包含要避免的反模式

**Example / 示例:**
```bash
python3 .agent/skills/ui-ux-pro-max/scripts/search.py "beauty spa wellness service" --design-system -p "Serenity Spa"
```

### Step 2b: Persist Design System / 第二步b：持久化设计系统

To save the design system for hierarchical retrieval across sessions, add `--persist` / 要保存设计系统以跨会话检索，请添加 `--persist`:

```bash
python3 .agent/skills/ui-ux-pro-max/scripts/search.py "<query>" --design-system --persist -p "Project Name"
```

### Step 3: Supplement with Detailed Searches / 第三步：补充详细搜索

After getting the design system, use domain searches to get additional details / 获取设计系统后，使用领域搜索获取更多细节:

```bash
python3 .agent/skills/ui-ux-pro-max/scripts/search.py "<keyword>" --domain <domain> [-n <max_results>]
```

| Need / 需求 | Domain / 领域 | Example / 示例 |
|---|---|---|
| More style options / 更多风格 | `style` | `--domain style "glassmorphism dark"` |
| Chart recommendations / 图表推荐 | `chart` | `--domain chart "real-time dashboard"` |
| UX best practices / UX 最佳实践 | `ux` | `--domain ux "animation accessibility"` |
| Alternative fonts / 替代字体 | `typography` | `--domain typography "elegant luxury"` |
| Landing structure / 落地页结构 | `landing` | `--domain landing "hero social-proof"` |

### Step 4: Stack Guidelines / 第四步：技术栈指南

Get implementation-specific best practices / 获取特定实现的最佳实践:

```bash
python3 .agent/skills/ui-ux-pro-max/scripts/search.py "<keyword>" --stack html-tailwind
```

Available stacks / 可用技术栈: `html-tailwind`, `react`, `nextjs`, `vue`, `svelte`, `swiftui`, `react-native`, `flutter`, `shadcn`, `jetpack-compose`
