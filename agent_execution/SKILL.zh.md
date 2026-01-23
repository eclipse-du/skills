---
name: Agent 执行管理器 (Agent Execution Manager)
description: 定义 Agent 如何执行任务以及与用户交互以获得批准的严格协议。
---

# Agent 执行管理器

## 目的
确保 Agent 在没有用户共识的情况下绝不行动，并且所有验证步骤都清晰明了，使用用户首选的语言。

## Agent 指令

### 1. 强制批准协议
*   **触发条件**: 在进入任何任务的 `EXECUTION` 模式之前，或在运行任何进行持久性更改（写入文件、运行命令）的工具之前。
*   **规则**: 在用户明确批准你的计划或任务列表之前，你**绝不**能继续。
*   **机制**:
    1.  **第一阶段 (任务定义)**: 创建 `task.md` 和 `task.zh.md`。
    2.  **停止并确认**: 立即调用 `notify_user`。**切勿**在此刻创建 `implementation_plan.md`。
    3.  **第二阶段 (计划)**: 任务获批后，创建 `implementation_plan.md`。
    4.  **停止并确认**: 再次调用 `notify_user` 以批准技术计划。
    5.  **第三阶段 (执行)**: 只有在计划获批后，才开始工作。

### 2. 中文确认偏好
*   **触发条件**: 当如上所述请求用户确认时。
*   **规则**: 你**必须**使用计划/任务列表的中文版本进行确认请求。
*   **机制**:
    *   如果你创建了 `implementation_plan.md` 和 `implementation_plan.zh.md`。
    *   在你的 `notify_user` 调用中，列出 `PathsToReview` 时，包含**两个**文件，但在你的消息中明确引用中文文件，或通过中文版本请求用户审查。
    *   *理由*: 用户更喜欢用中文确认执行细节。

### 3. 示例工作流
1.  **Planning Mode**: 分析请求。
2.  **Create Artifacts**: 创建 `task.md` 和 `task.zh.md`。
3.  **Request Approval**:
    *   调用 `notify_user(BlockedOnUser=true, ShouldAutoProceed=false, PathsToReview=[...task.zh.md, ...task.md])`。
    *   消息: "我已经准备好了任务列表，请您确认 `task.zh.md`。"
4.  **Wait**: 用户说 "ok"。
5.  **Execution Mode**: 开始工作。
