# Agent Skills Repository 🧠

欢迎来到本 Agent 的专属能力/技能库（Skills Repository）。这里定义了一系列强化 Agent 工作的操作流、指南规范和自定义增强工具，以赋能系统执行更高阶和自动化的任务处理。

## 🎯 核心能力一览 (Skills Overview)

以下是本项目当中囊括的所有技能以及其各自的作用说明：

### 🛠️ 通用工程流与任务框架
- **brainstorming**: 强制创意启动前缀器 —— 在正式实施任何新功能和修改行为前，对需求进行深挖并头脑风暴，探索更优设计。
- **writing-plans**: 大纲撰写 —— 在没有写任何一行代码之前，针对需求先行输出具体翔实的任务实施大纲。
- **planning-with-files**: 巨型任务处理核心（Manus 风格） —— 用于面对非常长链且复杂的重构任务，基于实体文件规划步骤与进度，且支持断点恢复。
- **subagent-driven-development**: 会话内 Sub-agent 并行驱动 —— 在同一个对话循环内调度和启用子 Agent 去分管并击破各分离模块。
- **dispatching-parallel-agents**: 无相依型并行代理执行者 —— 用于分配多个无直接依赖的任务给独立的智能体实现并行高效开发。
- **executing-plans**: 执行具体计划 —— 基于上述的开发大纲独立执行代码流，控制并反馈开发节点。
- **strategic-compact**: 战略性上文精简 —— 在长链任务阶段之间主动提议更优的上文合并清理以保护核心 Context。
- **using-superpowers**: 技能引导说明书 —— 用来教育当前 Agent 必须懂得查阅和唤醒其他高阶能力的顶层设定规则。
- **bilingual_docs**: 双语文档经理 —— 强制管理和落实所有 Markdown 沉淀文档都要具有中英双语的版本！

### 💻 编程语言、架构与范式
- **coding-standards**: 基础代码质量审查 —— TypeScript, JS, React, Node.js 等全栈的普遍高质量规范合集。
- **backend-patterns**: 后端模式最佳实践 —— Node.js/Express/Next.js 后端开发的高级架构、API规范与全场景性能调优指北。
- **frontend-patterns**: 前端模式指南 —— React 和 Next.js 的高阶组件范式、状态拦截逻辑和性能优化。
- **clickhouse-io**: ClickHouse 工程师 —— ClickHouse 的专项使用，囊括查询性能优化、全套开发模式和分析方案处理引擎。

### 🎨 UI/UX与自动化图表
- **ui-ux-pro-max**: 骨灰级高保真审美指南 —— 提供超过 50种设计风格、21套极致调色、字体搭配以及微动效组合，严禁交付廉价粗糙页面！
- **auto_mermaid**: Mermaid.js 全自动绘图器 —— 将过长的逻辑代码转换为直观的序列图、状态机或类图去向业务输出架构成果。
- **auto_tech_nano_banana**: 幻灯片自动化图库生成 —— 将代码逻辑与设计概念具象化，调用 AI 图片生成高质量的技术展示幻灯片组件。

### 🐞 测试、代码审查与质量管理
- **tdd-workflow**: TDD 极致重构向导 —— 强调在启动业务开发/重构前首先编写囊括 Unit, Integration 与 E2E ＞80% 覆盖程度的高可用测试。
- **test-driven-development**: 标准行为级 TDD —— 在功能实现或 bug 修复前先行撰写预期测试行为的标准模板。
- **systematic-debugging**: 系统化除虫师 —— 盲目打补丁克星！在分析 Error 前强制规范一套系统、可解释并且溯源分析的 Debug 排查框架。
- **verification-before-completion**: 结果唯一论验证器 —— 在承诺结束任务或者发布分支前，强制通过输出命令或真实截屏校验：所有声称的 Fixed 必须拿出铁证！
- **requesting-code-review**: 申请 Review 指南 —— 将完成的工作准备妥当进行交叉审计前须核查的所有代码规范卡点。
- **receiving-code-review**: 反查 Review 的理性代理 —— 在拿到他方 Review 修改建议时保持极高的技术定力进行验证，拒绝顺从性乱改。
- **code-review-router**: 混合模型 Code Review 评委 —— 基于语言技术栈和复杂度，智能抉择代码修改是由 Gemini 评价还是交由 Codex 介入反馈。
- **security-review**: 零信任防御巡警 —— 处理鉴权机制、密钥管理、数据加密及暴露端点时强制经过的安全审查机制单。

### 📁 项目、依赖与环境管理
- **code_manager**: 目录强迫症监理 —— 严守用户特定的目录层次安排机制进行项目拆解与部署。
- **using-git-worktrees**: 工作区隔离大使 —— 开展不稳定的 Feature 并拒绝污染既有分支的 `git worktree` 使用规范。
- **finishing-a-development-branch**: 分支终结处理法 —— 在合并特性之前的收尾行动说明（包含了 Rebase、测试覆盖及残留物清扫安排）。
- **fix-terminal-encoding**: 乱码救星 —— 环境乱码快速修复小技能：主动覆盖底层全局和会话以 UTF-8 输出以确保 Agent 读取终端。
- **agent_execution**: Agent 底层执行与审批经理 —— Agent 底层操作、执行系统命令等敏感动作时的请示机制。

### 📚 外部知识集成与私人工具
- **my-notion-writer**: 个人 Notion 知识库专属助理 —— 为用户直接将论文阅读体验、人生格言录和架构理论抓取沉淀到用户私有的 Notion Database 当中。
- **notebooklm-manager**: NotebookLM 大脑枢纽助手 —— 连接用户 NotebookLM 特定的商业模型环境进行知识拉取和汇总加工生成。
- **chinese-tts**: 中文语音生成合成器 —— 从文本生成高可用中文语音（使用自带的高级 TTS 工具 `tts_tool.py`），为项目和游戏打磨精妙拟人播音表现。
- **continuous-learning**: 自我净化与成长者 —— 赋予 Agent 在对话长河之中主动将有复用价值的操作保存沉淀、创建出新 `SKILL` 的超能力。
- **writing-skills**: 技能设计师 —— 提供编写与打样上述所有 Skill 的通用教程大纲。

---
> ⚠️ **提示：** Agent 每次需要调用这些能力时，都需要使用 `view_file` 或相关工具拉取对应子目录中的 `SKILL.md` 来获取完整操作大纲与技术细节！
