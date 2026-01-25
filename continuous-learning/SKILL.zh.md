---
name: continuous-learning
description: 自动从 Claude Code 会话中提取可重复使用的模式，并将其保存为已习得的技能以供未来使用。
---

# 持续学习技能 (Continuous Learning Skill)

在每次会话结束时自动评估 Claude Code 会话，提取可重复使用的模式，并将其保存为已习得的技能。

## 工作原理

此技能在每个会话结束时作为 **Stop hook** 运行：

1. **会话评估**：检查会话是否有足够的消息（默认：10+）
2. **模式检测**：从会话中识别可提取的模式
3. **技能提取**：将有用的模式保存到 `~/.claude/skills/learned/`

## 配置

编辑 `config.json` 进行自定义：

```json
{
  "min_session_length": 10,
  "extraction_threshold": "medium",
  "auto_approve": false,
  "learned_skills_path": "~/.claude/skills/learned/",
  "patterns_to_detect": [
    "error_resolution",
    "user_corrections",
    "workarounds",
    "debugging_techniques",
    "project_specific"
  ],
  "ignore_patterns": [
    "simple_typos",
    "one_time_fixes",
    "external_api_issues"
  ]
}
```

## 模式类型

| 模式 | 描述 |
|---------|-------------|
| `error_resolution` | 及其特定错误是如何解决的 |
| `user_corrections` | 来自用户纠正的模式 |
| `workarounds` | 对框架/库的怪癖的变通方法 |
| `debugging_techniques` | 有效的调试方法 |
| `project_specific` | 特定于项目的约定 |

## Hook 设置

添加到你的 `~/.claude/settings.json`：

```json
{
  "hooks": {
    "Stop": [{
      "matcher": "*",
      "hooks": [{
        "type": "command",
        "command": "~/.claude/skills/continuous-learning/evaluate-session.sh"
      }]
    }]
  }
}
```

## 为什么要用 Stop Hook?

- **轻量级**：在会话结束时仅运行一次
- **非阻塞**：不会给每条消息增加延迟
- **完整上下文**：可以访问完整的会话记录

## 相关内容

- [长篇指南 (The Longform Guide)](https://x.com/affaanmustafa/status/2014040193557471352) - 持续学习部分
- `/learn` 命令 - 会话中期的手动模式提取
