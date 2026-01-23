---
name: writing-plans
description: 当你有一个多步骤任务的规范或需求时，在编写代码之前使用此技能
---

# 编写计划 (Writing Plans)

## 概述

编写全面的实施计划，假设工程师对我们的代码库零背景且品味可疑。记录他们需要知道的一切：每个任务需要修改哪些文件、代码、测试、可能需要检查的文档，以及如何测试。将整个计划作为由一口大小的任务组成的列表提供给他们。DRY。YAGNI。TDD。频繁提交。

假设他们是一名熟练的开发人员，但几乎不了解我们的工具集或问题领域。假设他们不太了解良好的测试设计。

**开始时宣布：** "我正在使用 writing-plans 技能来创建实施计划。"

**上下文：** 这应该在一个专门的工作树（由 brainstorming 技能创建）中运行。

**计划保存至：** `docs/plans/YYYY-MM-DD-<feature-name>.md`

## 需求分析 (Requirement Analysis)

**在编写计划之前，你必须暂停并询问：**

识别 3 个关于需求的“跨度大”且“有深度”的问题。
- **跨度大 (Wide-spanning):** 涵盖不同的方面（例如，边缘情况 vs 性能 vs 用户体验）。
- **深度 (Deep):** 超越表面层次（例如，询问特定的故障模式，而不仅仅是“错误处理”）。

**示例：**
1. “如果第三方 API 在批量操作期间返回 429 速率限制错误，系统应该如何表现？我们应该实施指数退避还是立即失败？”
2. “对于数据可视化，我们需要支持通过 WebSocket 进行实时更新，还是在这个版本中轮询机制就足够了？”
3. “对于新的 UI 组件，是否有任何特定的无障碍合规标准（如 WCAG 2.1 AA）需要遵守？”

**在用户回答这些问题之前，不要继续编写计划。**

## 一口大小的任务粒度

**每个步骤是一个动作（2-5 分钟）：**
- "编写失败的测试" - 步骤
- "运行它以确保它失败" - 步骤
- "实现使测试通过的最小代码" - 步骤
- "运行测试并确保它们通过" - 步骤
- "提交" - 步骤

## 计划文档头部

**每个计划必须以这个头部开始：**

```markdown
# [功能名称] 实施计划

> **给 Claude:** 必需的子技能：使用 superpowers:executing-plans 来逐个任务地实施此计划。

**目标:** [一句话描述这构建了什么]

**架构:** [关于方法的 2-3 句话]

**技术栈:** [关键技术/库]

---
```

## 任务结构

```markdown
### 任务 N: [组件名称]

**文件:**
- 创建: `exact/path/to/file.py`
- 修改: `exact/path/to/existing.py:123-145`
- 测试: `tests/exact/path/to/test.py`

**步骤 1: 编写失败的测试**

```python
def test_specific_behavior():
    result = function(input)
    assert result == expected
```

**步骤 2: 运行测试以验证它失败**

运行: `pytest tests/path/test.py::test_name -v`
预期: 失败，提示 "function not defined"

**步骤 3: 编写最小实现**

```python
def function(input):
    return expected
```

**步骤 4: 运行测试以验证它通过**

运行: `pytest tests/path/test.py::test_name -v`
预期: 通过

**步骤 5: 提交**

```bash
git add tests/path/test.py src/path/file.py
git commit -m "feat: add specific feature"
```
```

## 记住
- 始终使用确切的文件路径
- 计划中包含完整的代码（不要只写“添加验证”）
- 确切的命令和预期的输出
- 使用 @ 语法引用相关技能
- DRY, YAGNI, TDD, 频繁提交

## 执行移交

保存计划后，提供执行选择：

**"计划已完成并保存至 `docs/plans/<filename>.md`。两个执行选项：**

**1. 子代理驱动 (Subagent-Driven) (当前会话)** - 我为每个任务分派新的子代理，在任务之间进行审查，快速迭代

**2. 并行会话 (Parallel Session) (单独)** - 使用 executing-plans 打开新会话，带检查点的批量执行

**选择哪种方法？"**

**如果选择子代理驱动：**
- **必需的子技能：** 使用 superpowers:subagent-driven-development
- 留在当前会话中
- 每个任务使用新的子代理 + 代码审查

**如果选择并行会话：**
- 引导他们在工作树中打开新会话
- **必需的子技能：** 新会话使用 superpowers:executing-plans
