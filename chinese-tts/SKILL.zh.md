---
name: chinese-tts
description: 当你需要使用指定的 `tts_tool.py` 将中文文本转换为语音 (TTS) 时，可以使用此技能。
---

# 中文语音合成 (TTS)

## 概述

此技能提供了一种标准化的方法，使用 `tts_tool.py` 工具生成高质量的中文语音音频。它确保了在不同上下文和项目中的一致性语音使用。

## 使用场景

-   **游戏资产**：生成角色对话、旁白或系统公告。
-   **旁白配音**：为视频或演示文稿创建音轨。
-   **系统通知**：生成系统警报或状态更新。
-   **原型开发**：快速创建用于开发的占位符音频。

## 语音选择指南

选择合适的语音对于用户体验至关重要。请使用以下映射表为您的上下文选择合适的语音：

### 男声
| Voice ID | 风格 | 上下文 / 场景 |
| :--- | :--- | :--- |
| `male_magnetic` | **磁性 (Default)** | **通用助手**、主角、友好的 NPC、用户交互。大多数男性角色的**默认选择**。 |
| `male_steady` | 沉稳 | **新闻播报**、正式公告、纪录片、严肃旁白。 |
| `male_deep` | 低沉 | **讲故事**、电影预告片、神秘角色、BOSS 战。 |
| `male_cheerful` | 欢快 | **游戏胜利**、喜庆活动、喜剧角色、充满活力的公告。 |

### 女声
| Voice ID | 风格 | 上下文 / 场景 |
| :--- | :--- | :--- |
| `female_gentle` | **温柔 (Default)** | **客服**、向导、治愈/安慰角色、睡前故事。大多数女性角色的**默认选择**。 |
| `female_steady` | 沉稳 | **新闻广播**、企业演示、教育内容、教学指南。 |
| `female_passionate` | 激情 | **戏剧角色**、强烈情感、紧急警报、动作场景。 |
| `female_cheerful` | 欢快 | **问候**、儿童内容、令人振奋的消息、社交媒体短视频。 |

> [!WARNING]
> **严格限制**：仅允许使用上述表格中列出的 8 个 Voice ID (`male_magnetic` ... `female_cheerful`)。严禁创造或使用任何其他 Voice ID (如 `zh-CN-XiaoxiaoNeural` 等)，会导致生成失败。必须严格遵守 `tts_tool.py` 中定义的映射。

## 用法

使用 `run_command` 工具执行 `tts_tool.py`。

```bash
python d:\du\code\user_projects\tools\tts_tool.py "<TEXT>" "<OUTPUT_FILE_PATH>" --voice <VOICE_ID> --format <FORMAT>
```

### 参数

-   `<TEXT>`: 要转换的中文文本。保持简洁以获得更好的效果。
-   `<OUTPUT_FILE_PATH>`: 保存音频文件的绝对路径。
-   `--voice`: (可选) 上表中对应的 Voice ID。默认为 `male_magnetic`。
-   `--format`: (可选) `mp3` (默认) 或 `wav`。`wav` 适用于无损/编辑，`mp3` 适用于网络/移动端。

## 最佳实践

### 1. 命名规范
-   **Agent 决定**：你 (Agent) 充当创意总监。选择能够反映内容和目的的文件名。
-   **格式**：**英文** `snake_case`。
-   **结构**：`[类别]_[内容摘要]_[语音风格]`。
    -   *示例*：`intro_welcome_message_female_gentle.mp3`
    -   *示例*：`level_1_victory_male_cheerful.wav`

### 2. 路径管理
-   **一致性**：将音频文件存储在项目内的专用目录中，通常为 `assets/audio`、`resources/sound` 或 `static/media`。
-   **项目感知**：尊重现有的项目结构。如果是快节奏游戏，可能是 `assets/sfx/voice`。如果是 Web 应用，可能是 `public/audio`。

### 3. 工作流程
1.  **分析上下文**：确定文本的情感和目的。
2.  **选择语音**：从“语音选择指南”表中选择最合适的语音。
3.  **生成**：运行命令。
4.  **验证**：检查文件是否只需成功创建。
