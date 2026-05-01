---
name: requirement-clarification
description: |
  Use this skill whenever the user's input is vague, colloquial, fragmented, or ambiguous.
  Trigger on phrases like "我想要一个...", "帮我弄个...", "能不能做...", "有没有办法...",
  "我想弄个...", or any natural-language request that needs restructuring into clear,
  actionable requirements. This skill converts raw user speech into precise,
  AI-understandable specifications for use in hemer agent conversations. Output only
  the clarified content without any explanatory text, commentary, or formatting artifacts.

---

# Requirement Clarification

Convert vague, colloquial, or fragmented user input into clear, structured requirements
that an AI agent can accurately interpret and act upon. Designed for hemer agent
conversation pipelines.

## When to use this skill

Trigger this skill whenever the user expresses a need, request, or question in
natural, conversational language — especially when the phrasing is:

- Colloquial or informal
- Fragmented or incomplete
- Ambiguous or open to interpretation
- Mixed with irrelevant details

## Workflow

1. Receive the user's raw input exactly as spoken or typed.
2. Strip all filler words, conversational padding, and non-essential content.
3. Extract the core intent and any parameters, constraints, or preferences mentioned.
4. Reconstruct the requirement in concise, structured, declarative language.
5. Output ONLY the clarified requirement. Add nothing else.

## Output rules

- No introductory phrases
- No closing remarks or follow-up questions
- No markdown formatting, code fences, or JSON wrappers
- No bullet points or numbered lists unless the original request explicitly contains a list
- No brackets, labels, or metadata around the output
- Output exactly one line or one short paragraph of plain text

## Examples

**Input:** 我想弄个能记录每天花多少钱的东西，最好还能分类
**Output:** 创建一个日常消费记录工具，支持按类别分类管理支出。

**Input:** 帮我做个自动发邮件的，到时间就发
**Output:** 开发一个定时自动发送邮件的功能。

**Input:** 有没有什么办法能让我的网站加载更快，现在太慢了
**Output:** 优化网站加载速度，提升前端性能。

**Input:** 我想要一个app，就是那种可以拍照识别植物是什么的
**Output:** 开发一款移动应用，通过拍照识别植物种类。

**Input:** 能不能帮我整理一下上个月的销售数据，看看哪些产品卖得好
**Output:** 分析上个月销售数据，按产品销量排序并找出畅销产品。

## Available resources

No bundled resources. This skill is self-contained and operates purely through
instruction following.