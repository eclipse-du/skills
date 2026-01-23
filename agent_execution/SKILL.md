---
name: Agent Execution Manager
description: Defines strict protocols for how the agent executes tasks and interacts with the user for approval.
---

# Agent Execution Manager

## Purpose
To ensure that the agent never acts without user consensus and that all verification steps are clear, in the user's preferred language.

## Instructions for the Agent

### 1. Enforced Approval Protocol (CRITICAL)
*   **Trigger**: Before entering the `EXECUTION` mode of any task, or before running any tool that makes persistent changes (writing files, running commands).
*   **Use Case**: Even if the user says "Ok" to a general idea, you must present a concrete plan and get approval *again* before writing code.
*   **Rule**: You **MUST NOT** proceed until the user has explicitly approved your plan or task list.
*   **Mechanism**:
    1.  **Phase 1 (Task Definition)**: Create `task.md` and `task.zh.md`.
    2.  **STOP & CONFIRM**: Call `notify_user` immediately. Do NOT create `implementation_plan.md` yet.
    3.  **Phase 2 (Planning)**: Once tasks are approved, create `implementation_plan.md` and `implementation_plan.zh.md`.
    4.  **STOP & CONFIRM**: Call `notify_user` again to approve the technical plan.
    5.  **Phase 3 (Execution)**: Only after Plan is approved, proceed to work.

### 2. Chinese Confirmation Preference
*   **Trigger**: When asking for the user's confirmation as described above.
*   **Rule**: You **MUST** use the Chinese version of the plan/task list for the confirmation request.
*   **Mechanism**:
    *   If you have created `implementation_plan.md` and `implementation_plan.zh.md`.
    *   In your `notify_user` call, when listing `PathsToReview`, include **BOTH** files but explicitly reference the Chinese file in your message or ask the user to review the Chinese version.
    *   *Reasoning*: The user prefers to confirm execution details in Chinese.

### 3. Example Workflow
1.  **Planning Mode**: Analyze request.
2.  **Create Artifacts**: Create `task.md` and `task.zh.md`.
3.  **Request Approval**:
    *   Call `notify_user(BlockedOnUser=true, ShouldAutoProceed=false, PathsToReview=[...task.zh.md, ...task.md])`.
    *   Message: "我已经准备好了任务列表，请您确认 `task.zh.md`。(I have prepared the task list, please confirm `task.zh.md`.)"
4.  **Wait**: User says "ok".
5.  **Execution Mode**: Start work.

### 4. Environment Verification & Troubleshooting (CRITICAL)
*   **Trigger**: When running any terminal command (`run_command`).
*   **Rule**: You **MUST** monitor the output for garbled text (乱码), such as `` or unreadable characters.
*   **Action**: If garbled text occurs, you **MUST IMMEDIATELY** execute the instructions in `Terminal Encoding Fixer` (e.g., run `chcp 65001`).
*   **Proactive**: If executing a command known to have encoding issues (like Gradle on Windows), proactively apply the fix *before* or *during* the command execution.
