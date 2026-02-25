---
name: my-notion-writer
description: Use when you need to record a quick thought, save a quote, write a detailed book/paper note, or log a theory/model note to Notion (e.g., to "毒鸡汤", "论文库", or "理论集合")
---

# My Notion Writer

## Overview
This skill centralizes and automates writing structured content to specific Notion pages and databases. By caching the Notion Page IDs and Database IDs inside this skill, we eliminate the need to search Notion every time, ensuring fast, consistent, and reliable formatting.

## When to Use

- When the user says "记录一个蛋疼的话", "存一句名人名言" → Route to **Resource/毒鸡汤**
- When the user says "帮我写个论文笔记", "整理这篇paper" → Route to **Area/论文库**
- When the user says "记一个理论笔记", "存个模型笔记", "整理XX理论" → Route to **理论集合**
- *Do not use this* for general search or non-predefined Notion targets unless the user explicitly asks to add a new category to this skill's cache.

## Working with the Cache

### Current Cache
Use these IDs directly instead of searching:

```yaml
destinations:
  "名言/碎碎念":
    target_type: "page"
    id: "e8f508eb-2d1e-4853-83e3-f77028c6bee2"
    name: "Resource/毒鸡汤"
    format: "Divider followed by a paragraph containing the quote/text."
  "论文笔记":
    target_type: "database"
    id: "724156ee-789d-4c26-b227-005d6d80f9ce"
    name: "Area/论文库"
    format: "A structured page within the database using predefined headings (🎯 核心观点, 🔔 思考心得, etc.)."
  "理论笔记/模型笔记":
    target_type: "database"
    id: "cd06b084-edac-42a4-856a-e085171219d2"
    name: "理论集合"
    format: "A new page in the database with properties only (Name, Tags, 释义, 思考, 实操, 🐂🍺🈯️). No page body content needed."
```

### Expanding the Cache
If the user asks to add a new destination (e.g., "以后我的反思都存到 X 页面"), **you must update this SKILL.md file** to append the new ID and formatting rules to the YAML cache above.

## Content Formatting Templates

**GLOBAL RULE:** All notes written to Notion MUST be in Chinese, even if the source material is in English, unless the user explicitly requests another language. Do not attempt to upload images to Notion; simply skip any images.

**GLOBAL TITLE RULE:** ALL `Name`/title fields across ALL destinations MUST NOT contain parentheses. Keep titles as short and punchy as possible — a reader should instantly grasp the topic without extra decoration.

### 1. 毒鸡汤 (Quotes / Random Thoughts)
**Target:** Append to page `e8f508eb-2d1e-4853-83e3-f77028c6bee2`
**Structure:**
1. A `divider` block.
2. A `paragraph` block containing the text.

**API Example:**
```json
{
  "block_id": "e8f508eb-2d1e-4853-83e3-f77028c6bee2",
  "children": [
    { "type": "divider", "divider": {} },
    { "type": "paragraph", "paragraph": { "rich_text": [{ "type": "text", "text": { "content": "Your quote here" } }] } }
  ]
}
```

### 2. 论文库 (Paper Notes)
**Target:** Create a new page in database `724156ee-789d-4c26-b227-005d6d80f9ce`
**Page Properties (Include as many as possible when creating/updating):**
- `Name` (title): The name of the paper WITHOUT the ID in parentheses (e.g., "KTO: Model Alignment as Prospect Theoretic Optimization"). No one remembers the arXiv ID.
- `URL` (url): The arxiv or source link.
- `Released Date` (date): The publish date of the paper (Format: YYYY-MM-DD).
- `Tags` (multi_select): You can dynamically generate new tags based on the paper's specific needs. (Existing reference options include: `NLU`, `LLM`, `RL`, etc.)
- `Group` (select): Represents the institution of the paper. Use existing options if they match (e.g., `Deepmind`, `Google`, `Anthropic`, `openAI`), or generate a new one if it is a new institution.
- `Related-product` (multi_select): Available options: `bot`, `search`, `KG`, `nlp_platform`, `IKC`.
- `Paper Name` (rich_text): The exact English/original title of the paper.
- `Number` (number): A score for the paper. Read the paper and provide a draft score (max of 5 score).

**Page Content Structure (RLVA_fail style):**
Use these exact Heading 1 blocks with specified background colors, followed by bulleted or numbered lists. **CRITICAL: You must provide deep, insightful analysis, not superficial summaries. Avoid "流水账" (running accounts).**

1. `🎯 核心观点` (green_background)
   - Numbered list. Must clearly highlight the paper's **innovation and core independent claims** compared to prior work. Do not just state what the paper is about.
2. `🔔 思考心得` (red_background)
   - Bulleted list. Provide **deep, critical thoughts**, limitations, or profound insights on how it integrates with products/frameworks.
3. `📔 对比分析` (purple_background)
   - Bulleted list of contributions vs limitations, or comparisons with competing paradigms.
4. `💗 痛点分析` (blue_background)
   - Numbered list of specific, technical problems addressed.
5. `🔔 算法架构` (red_background)
   - Bulleted list. Go **deep into the algorithmic design**, architecture mechanics, or taxonomies proposed, instead of high-level generalizations.
6. `🪖 实验结果` (brown_background)
   - Bulleted list. Must include **concrete metrics, numbers, benchmark scales, statistical conclusions, or tabular comparisons**. Do not use vague statements. (If it is a survey paper, summarize the concrete scales of benchmarks or framework comparisons).

### 3. 理论集合 (Theory / Model Notes)
**Target:** Create a new page in database `cd06b084-edac-42a4-856a-e085171219d2`
**Page Properties (ALL are required when creating):**
- `Name` (title): The name of the theory/model. **No parentheses allowed.** Keep it short and immediately recognizable (e.g., "阿德勒个体心理学", "混沌理论").
- `Tags` (multi_select): Categorization tag. Existing options include: `心理学`, `学习方法`, `工作`, `经济学`, `计算机`, `火箭学`, `宗教`, `成事心法`, `生物学`, `教育学`. You may also dynamically create new tags as needed.
- `释义` (rich_text): A concise definition/explanation of the theory. Keep it sharp and insightful.
- `思考` (rich_text): Deep personal reflection that expands on the 释义 — critically analyze the theory's implications, limitations, and how it connects to life/work. **Must go deep, not superficial.** Deconstruct each key concept in the 释义 chain and explain WHY it matters.
- `实操` (rich_text): Concrete, actionable advice or exercises derived from this theory. Give the reader a specific thing they can DO today. **If there are multiple points, separate them with `\n` line breaks** for readability in Notion (e.g., "Point 1\nPoint 2\nPoint 3"). Each point should have a bold label prefix.
- `🐂🍺🈯️` (number): A "coolness" score for the theory (max 5, decimals allowed, e.g., 4.6).

**No page body content is needed.** All information is stored purely in the database properties.

**API Example:**
```json
{
  "parent": { "database_id": "cd06b084-edac-42a4-856a-e085171219d2" },
  "properties": {
    "Name": { "title": [{ "text": { "content": "混沌理论" } }] },
    "Tags": { "multi_select": [{ "name": "生物学" }] },
    "释义": { "rich_text": [{ "text": { "content": "世界就是由噪声组成的，有序的才是非常态" } }] },
    "思考": { "rich_text": [{ "text": { "content": "生命只是巧合，混沌即是本质，参考第一性尤为重要" } }] },
    "实操": { "rich_text": [{ "text": { "content": "面对不确定性时，不要试图预测结果，而是建立对混沌的容忍度..." } }] },
    "🐂🍺🈯️": { "number": 4.6 }
  }
}
```

## Workflow: User Confirmation

**CRITICAL:** Because writing to Notion is a state-mutating operation, you **MUST** follow this explicit confirmation workflow:

1. **Draft:** Generate the exact content you plan to write to Notion.
2. **Preview:** Use `notify_user` to show the drafted Markdown to the user.
   - For quotes, show the quote.
   - For papers, show the structured headings and bullet points.
3. **Wait for OK:** Do NOT call the Notion API until the user explicitly confirms (e.g., replies with "OK", "没问题", "写吧").
4. **Execute:** Do NOT rewrite API calling code or use the MCP server directly if it is unstable. Instead, write your JSON payload to a temporary file (e.g., `payload.json`) and execute the consolidated Python script:
   `python d:\du\code\user_projects\tools\upload_to_notion.py <payload.json> [POST|PATCH] [url_path]`
   For creating a page, omit the last two arguments. For updating a page, use `PATCH` and `/v1/pages/<page_id>`.
5. **Verify & Report:** Confirm the API call succeeded and inform the user "注入成功!".

## Common Mistakes

- **Writing without confirmation:** Never silently write to Notion. Always show the draft first.
- **Forgetting the divider in 毒鸡汤:** The page will look messy if quotes run together. Always prepend a divider block.
- **Using wrong block types for Paper Notes:** Notion requires specific JSON structures for colored headings and lists. Ensure you format the API payload correctly (e.g., `"heading_1": { "color": "green_background" }`).
- **Searching instead of using the cache:** Do not use `mcp_notion-mcp-server_API-post-search`. Use the IDs hardcoded in this skill.
