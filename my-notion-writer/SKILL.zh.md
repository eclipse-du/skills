---
name: my-notion-writer
description: 当你需要快速记录想法、保存名人名言，或者将详细的读书/论文笔记写入 Notion（如“毒鸡汤”或“论文库”）时使用本技能。
---

# My Notion Writer (Notion 定制写入工具)

## 技能概述
本技能旨在将结构化内容集中并自动化地写入到特定的 Notion 页面和 Database 中。通过在此技能内部缓存 Notion 的 Page ID 和 Database ID，我们无需每次在 Notion 中搜索，从而保证了写入操作的快速、格式一致且可靠。

## 什么时候使用

- 当用户说：“记录一个蛋疼的话”、“存一句名人名言” → 路由至 **Resource/毒鸡汤**
- 当用户说：“帮我写个论文笔记”、“整理这篇paper” → 路由至 **Area/论文库**
- *请勿将此技能用于*一般的 Notion 搜索，或写入非预设的目标页面，除非用户明确要求将新类别添加到此技能的缓存中。

## 缓存工作机制

### 当前缓存列表
请直接使用以下 ID，不要通过 API 重新搜索：

```yaml
destinations:
  "名言/碎碎念":
    target_type: "page"
    id: "e8f508eb-2d1e-4853-83e3-f77028c6bee2"
    name: "Resource/毒鸡汤"
    format: "插入一个分割线 (divider)，随后插入一个包含引言/文本的段落 (paragraph)。"
  "论文笔记":
    target_type: "database"
    id: "724156ee-789d-4c26-b227-005d6d80f9ce"
    name: "Area/论文库"
    format: "在 Database 中创建一个结构化页面，使用预设的标题区块 (🎯 核心观点, 🔔 思考心得 等)。"
```

### 扩充缓存
如果用户要求添加新的目标写入地（例如：“以后我的反思都存到 X 页面”），**你必须更新这个 `SKILL.md` / `SKILL.zh.md` 文件**，将新的 ID 和排版规则追加到上面的 YAML 缓存中。

## 内容排版模板

**全局规则:** 写入 Notion 的所有笔记内容**必须统一使用中文**（即使原文是英文），除非用户明确要求使用其他语言。如果原文包含图片，直接跳过，**不要尝试上传任何图片**。

### 1. 毒鸡汤 (引言 / 碎碎念)
**写入目标:** 追加到页面 `e8f508eb-2d1e-4853-83e3-f77028c6bee2` 的末尾
**区块结构:**
1. 一个 `divider` (分割线) 区块。
2. 一个 `paragraph` (普通段落) 区块，包含具体文本。

**API Payload 示例:**
```json
{
  "block_id": "e8f508eb-2d1e-4853-83e3-f77028c6bee2",
  "children": [
    { "type": "divider", "divider": {} },
    { "type": "paragraph", "paragraph": { "rich_text": [{ "type": "text", "text": { "content": "你的引言内容" } }] } }
  ]
}
```

### 2. 论文库 (学术/书籍笔记)
**写入目标:** 在 Database `724156ee-789d-4c26-b227-005d6d80f9ce` 中创建一个新页面 (Page)
**页面属性 (Properties) - 创建/更新页面时请尽可能填写以下属性:**
- `Name` (title/标题): 论文的名称。**注意：标题中不要带括号里的内容（如 arXiv ID 等），没人记得住 ID**（例如 "KTO: Model Alignment..."）。
- `URL` (url/链接): 论文来源网址或 arXiv 链接。
- `Released Date` (date/日期): 论文发布日期 (格式: YYYY-MM-DD)。
- `Tags` (multi_select/多选标签): 可以基于文章需求动态生成，不一定要局限于已有选项。（已有参考选项包括: `NLU`, `LLM`, `RL` 等）。
- `Group` (select/单选分类): 代表这篇论文的机构。如果已有选项匹配（如 `Deepmind`, `Facebook`, `Google` 等），请直接使用；如果是其他新机构，你可以动态新增。
- `Related-product` (multi_select/多选产品关联): 可选值包括: `bot`, `search`, `KG`, `nlp_platform`, `IKC`。
- `Paper Name` (rich_text/副标题): 论文完整的原始英文标题。
- `Number` (number/数值): 这篇文章的评分。请在你读完论文后，自己打一个草稿分数（满分5分）。

**页面正文结构 (参照 RLVA_fail 模板):**
页面生成后，使用 `patch-block-children` 追加以下内容。必须使用精准的 Heading 1 标题和指定的背景颜色，其后跟随无序或有序列表。
**绝对强制要求：必须深入思考，提供干货，绝不能写成表面文章或流水账。**

1. `🎯 核心观点` (color: "green_background")
   - 用编号列表。**必须明确点出该文章相较于其他paper的创新点和核心独立性观点**，而不仅仅是概括文章内容。
2. `🔔 思考心得` (color: "red_background")
   - 用无序列表。提供**深入的、批判性的思考**、产品结合的重度见解或局限性洞察。
3. `📔 对比分析` (color: "purple_background")
   - 用无序列表深度剖析“贡献”和“不足”，或与其他范式的硬核对比。
4. `💗 痛点分析` (color: "blue_background")
   - 用编号列表记录其致力于解决的具体技术痛点。
5. `🔔 算法架构` (color: "red_background")
   - 用无序列表。**必须深入探讨算法架构细节、底层逻辑或推导体系**，拒绝浅尝辄止的泛泛而谈。
6. `🪖 实验结果` (color: "brown_background")
   - 用无序列表。**必须要包含具体的数字指标、Benchmark规模、表格数据总结或硬核实验结论**，坚决杜绝没有数据的负面典型。（如果是综述文章，则列出其统计的Benchmark指标维度、框架对比数据等客观事实）。

## 工作流：用户强制确认机制

**极其重要 (CRITICAL):** 因为向 Notion 写入数据是具有副作用的操作，你**必须**遵循以下显式确认流程：

1. **起草内容:** 准备好你计划写入 Notion 的具体 Markdown 文本或 JSON 结构。
2. **预览确认:** 使用 `notify_user` 工具，在终端中向用户展示草稿内容。
   - 对于毒鸡汤引言，展示加好 Markdown 格式的引语。
   - 对于论文笔记，展示包含所有固定标题和要点列表的结构化大纲。
3. **等待最终OK:** **在用户明确回复确认前（如回复“OK”、“没问题”、“写吧”），绝对不要调用 Notion API**。
4. **执行写入:** 用户确认后，**不要重新编写代码**，也不要直接依赖可能不稳定的 MCP 工具。请将打包好的 JSON payload 写入一个本地临时文件（例如 `payload.json`），然后调用固化的、最高效的 Python 脚本：
   `python d:\du\code\user_projects\tools\upload_to_notion.py <payload.json> [POST|PATCH] [url_path]`
   如果是创建新页面，可省略后两个参数；若是更新页面，则传 `PATCH` 和 `/v1/pages/<page_id>`。
5. **验证与反馈:** 确认 API 调用成功后，向用户反馈“注入成功！”。

## 常见错误与防范

- **未经确认直接写入:** 永远不要静默吸入 Notion。总是先弹出草稿预览。
- **毒鸡汤忘记加分割线:** 连续写入会导致排版粘连、混乱，必须在每段话前强制前置 `divider` 区块。
- **论文库笔记的区块类型错误:** Notion 对彩色标题和列表有严格的 JSON 格式要求，请务必确保 Payload 的准确性（例如：`"heading_1": { "color": "green_background" }`）。
- **盲目搜索:** 不要使用 `mcp_notion-mcp-server_API-post-search` 盲搜，一定要直接使用本技能中硬编码缓存的 ID。
