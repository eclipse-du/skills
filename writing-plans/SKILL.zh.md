---
name: writing-plans
description: 当你有多步任务的规范或需求时，在接触代码之前使用
---

# 编写计划

## 概述

编写全面的实施计划，假设工程师对我们的代码库零上下文且品味可疑。记录他们需要知道的一切：每个任务要接触哪些文件、代码、测试、他们可能需要检查的文档、如何测试它。将整个计划作为小任务给他们。DRY。YAGNI。TDD。频繁提交。

假设他们是一个熟练的开发人员，但如果不了解我们的工具集或问题域。假设他们不太了解好的测试设计。

**开始时宣布：** "我正在使用 writing-plans 技能来创建实施计划。"

**上下文：** 这应该在一个专用的 worktree 中运行（由 brainstorming 技能创建）。

**保存计划到：** `docs/plans/YYYY-MM-DD-<feature-name>.md`

## 小任务粒度

**每一步是一个动作（2-5 分钟）：**
- "编写失败测试" - 步骤
- "运行它以确保它失败" - 步骤
- "实施使测试通过的最少代码" - 步骤
- "运行测试并确其通过" - 步骤
- "提交" - 步骤

## 计划文档头

**每个计划必须以这个头开始：**

```markdown
# [Feature Name] Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** [一句话描述这构建了什么]

**Architecture:** [关于方法的 2-3 句话]

**Tech Stack:** [关键技术/库]

---
```

## 任务结构

```markdown
### Task N: [Component Name]

**Files:**
- Create: `exact/path/to/file.py`
- Modify: `exact/path/to/existing.py:123-145`
- Test: `tests/exact/path/to/test.py`

**Step 1: Write the failing test**

```python
def test_specific_behavior():
    result = function(input)
    assert result == expected
```

**Step 2: Run test to verify it fails**

Run: `pytest tests/path/test.py::test_name -v`
Expected: FAIL with "function not defined"

**Step 3: Write minimal implementation**

```python
def function(input):
    return expected
```

**Step 4: Run test to verify it passes**

Run: `pytest tests/path/test.py::test_name -v`
Expected: PASS

**Step 5: Commit**

```bash
git add tests/path/test.py src/path/file.py
git commit -m "feat: add specific feature"
```
```

##以此为记
- 始终使用确切的文件路径
- 计划中的完整代码（不是“添加验证”）
- 带有预期输出的确切命令
- 使用 @ 语法引用相关技能
- DRY, YAGNI, TDD, 频繁提交

## 执行交接

保存计划后，提供执行选择：

**"计划完成并保存到 `docs/plans/<filename>.md`。两个执行选项：**

**1. Subagent-Driven (本次会话)** - 我为每个任务调度新的子代理（subagent），在任务之间审查，快速迭代

**2. Parallel Session (单独)** - 使用 executing-plans 打开新会话，带有检查点的批量执行

**哪种方法？"**

**如果选择 Subagent-Driven：**
- **必须的子技能：** 使用 superpowers:subagent-driven-development
- 留在本次会话
- 每个任务新的子代理 + 代码审查

**如果选择 Parallel Session：**
- 引导他们在 worktree 中打开新会话
- **必须的子技能：** 新会话使用 superpowers:executing-plans
