---
name: requesting-code-review
description: 当完成任务、实施主要功能或在合并之前使用，以验证工作是否符合要求
---

# 请求代码审查

调度 superpowers:code-reviewer 子代理（subagent），以在问题级联之前捕获它们。

**核心原则：** 尽早审查，经常审查。

## 何时请求审查

**强制性：**
- 在 subagent-driven development 中的每个任务之后
- 在完成主要功能之后
- 在合并到 main 之前

**可选但有价值：**
- 当卡住时（新的视角）
- 在重构之前（基线检查）
- 修复复杂错误之后

## 如何请求

**1. 获取 git SHA：**
```bash
BASE_SHA=$(git rev-parse HEAD~1)  # or origin/main
HEAD_SHA=$(git rev-parse HEAD)
```

**2. 调度 code-reviewer 子代理：**

使用 Task 工具和 superpowers:code-reviewer 类型，填写 `code-reviewer.md` 模板

**占位符：**
- `{WHAT_WAS_IMPLEMENTED}` - 你刚刚构建了什么
- `{PLAN_OR_REQUIREMENTS}` - 它应该做什么
- `{BASE_SHA}` - 起始 commit
- `{HEAD_SHA}` - 结束 commit
- `{DESCRIPTION}` - 简要总结

**3. 根据反馈采取行动：**
- 立即修复关键（Critical）问题
- 在继续之前修复重要（Important）问题
- 记录次要（Minor）问题以供稍后处理
- 如果审查者错误，请反驳（带有理由）

## 示例

```
[刚刚完成任务 2：添加验证功能]

你：让我在继续之前请求代码审查。

BASE_SHA=$(git log --oneline | grep "Task 1" | head -1 | awk '{print $1}')
HEAD_SHA=$(git rev-parse HEAD)

[调度 superpowers:code-reviewer 子代理]
  WHAT_WAS_IMPLEMENTED: 对话索引的验证和修复功能
  PLAN_OR_REQUIREMENTS: docs/plans/deployment-plan.md 中的任务 2
  BASE_SHA: a7981ec
  HEAD_SHA: 3df7661
  DESCRIPTION: 添加了 verifyIndex() 和 repairIndex() 以及 4 种问题类型

[子代理返回]:
  优势: 架构清晰，真实测试
  问题:
    重要: 缺少进度指示器
    次要: 报告间隔的魔法数字 (100)
  评估: 准备好继续

你：[修复进度指示器]
[继续到任务 3]
```

## 与工作流集成

**Subagent-Driven Development:**
- 每个任务之后审查
- 在问题复合之前捕获它们
- 在移动到下一个任务之前修复

**Executing Plans:**
- 每批次（3 个任务）后审查
- 获取反馈，应用，继续

**Ad-Hoc Development:**
- 合并前审查
- 卡住时审查

## 危险信号

**绝不：**
- 因为“这很简单”而跳过审查
- 忽略关键问题
- 在未修复重要问题的情况下继续
- 与有效的技术反馈争论

**如果审查者错误：**
- 用技术推理反驳
- 展示证明其有效的代码/测试
- 请求澄清

参见模板：requesting-code-review/code-reviewer.md
