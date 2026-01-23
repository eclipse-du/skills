---
name: Auto-Tech-Nano-Banana (自动技术香蕉)
description: 分析代码库并使用 AI 图像生成创建专业的技术演示幻灯片（图像）。
---

# Auto-Tech-Nano-Banana (自动技术香蕉)

## 目的
自动生成高质量的视觉“幻灯片”（图像），总结项目的价值、架构和技术栈。这对于快速汇报和分享技术概览非常有用。

## Agent 指令

**触发条件**: 当用户请求“香蕉报告”、“幻灯片”、“项目概览图”或明确调用该技能时。

### 1. 分析阶段
*   **扫描代码库**: 阅读 `README.md`, 架构文档, `build.gradle`/`package.json`, 和核心源文件。
*   **提取关键信息**:
    *   **价值**: 项目是做什么的？为谁服务的？关键功能？
    *   **架构**: 它是如何组织的？(MVVM, Clean Arch 等) 数据流？
    *   **技术栈**: 语言, 框架, 关键库。

### 2. 生成阶段 (图像生成)
你必须使用 `generate_image` 工具精确生成 3 张图像。

**通用风格规则**:
*   **风格**: 专业执行, 扁平化设计, 现代 UI/UX 演示风格。
*   **纵横比**: 16:9 (横向)。
*   **视觉效果**: 清晰的排版, 示意图, 图标。

#### 幻灯片 1: 项目价值 (Project Value)
*   **文件名**: `project_value`
*   **内容**: 项目标题, 标语, 3-4 个关于关键优势/功能的要点。
*   **Prompt 策略**: "A presentation slide titled '[Project Name]'. Modern design. Bullet points: [Benefit 1], [Benefit 2]..."

#### 幻灯片 2: 项目架构 (Project Architecture)
*   **文件名**: `project_architecture`
*   **内容**: 可视化系统的高层框图或流程图。
*   **Prompt 策略**: "A technical architecture diagram for [Project]. Layers: [Layer 1], [Layer 2]. Arrows showing data flow. Professional schematic style."

#### 幻灯片 3: 技术栈 (Tech Stack)
*   **文件名**: `tech_stack`
*   **内容**: 所用技术的 Logo 或列表（例如 Kotlin, Jetpack Compose, Gemini）。
*   **Prompt 策略**: "A presentation slide displaying a technology stack. Icons/Text for: [Tech 1], [Tech 2], [Tech 3]. Grid layout, clean background."

### 3. 输出规则
*   **保存位置**: 你**必须**将所有生成的图像保存到 `[ProjectRoot]/img_ai_gen/report/` 目录下。
*   **命名**: 确保文件名与上述描述匹配 (`project_value`, `project_architecture`, `tech_stack`).
