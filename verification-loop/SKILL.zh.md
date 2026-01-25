---
name: verification-loop
description: Claude Code 会话的综合验证系统。
---

# 验证循环技能 (Verification Loop Skill)

Claude Code 会话的综合验证系统。

## 何时使用

在以下情况下调用此技能：
- 完成功能或重大代码更改后
- 创建 PR 之前
- 当你想确保通过质量关口时
- 重构后

## 验证阶段

### 第一阶段：构建验证
```bash
# 检查项目是否构建
npm run build 2>&1 | tail -20
# 或者
pnpm build 2>&1 | tail -20
```

如果构建失败，请在继续之前停止并通过修复。

### 第二阶段：类型检查
```bash
# TypeScript 项目
npx tsc --noEmit 2>&1 | head -30

# Python 项目
pyright . 2>&1 | head -30
```

报告所有类型错误。在继续之前修复关键错误。

### 第三阶段：Lint 检查
```bash
# JavaScript/TypeScript
npm run lint 2>&1 | head -30

# Python
ruff check . 2>&1 | head -30
```

### 第四阶段：测试套件
```bash
# 运行带覆盖率的测试
npm run test -- --coverage 2>&1 | tail -50

# 检查覆盖率阈值
# 目标：最低 80%
```

报告：
- 总测试数: X
- 通过: X
- 失败: X
- 覆盖率: X%

### 第五阶段：安全扫描
```bash
# 检查机密信息
grep -rn "sk-" --include="*.ts" --include="*.js" . 2>/dev/null | head -10
grep -rn "api_key" --include="*.ts" --include="*.js" . 2>/dev/null | head -10

# 检查 console.log
grep -rn "console.log" --include="*.ts" --include="*.tsx" src/ 2>/dev/null | head -10
```

### 第六阶段：差异审查 (Diff Review)
```bash
# 显示更改内容
git diff --stat
git diff HEAD~1 --name-only
```

审查每个更改的文件以查找：
- 意外更改
- 缺少错误处理
- 潜在边缘情况

## 输出格式

运行所有阶段后，生成验证报告：

```
VERIFICATION REPORT
==================

Build:     [PASS/FAIL]
Types:     [PASS/FAIL] (X errors)
Lint:      [PASS/FAIL] (X warnings)
Tests:     [PASS/FAIL] (X/Y passed, Z% coverage)
Security:  [PASS/FAIL] (X issues)
Diff:      [X files changed]

Overall:   [READY/NOT READY] for PR

Issues to Fix:
1. ...
2. ...
```

## 连续模式

对于长会话，每 15 分钟或重大更改后运行验证：

```markdown
设置一个心理检查点：
- 完成每个函数后
- 完成组件后
- 移动到下一个任务前

运行: /verify
```

## 与 Hooks 集成

此技能是对 PostToolUse hooks 的补充，但提供了更深入的验证。
Hooks 立即捕获问题；此技能提供全面审查。
