---
name: sanko-sales
description: Use for any question about Sanko sales, revenue, orders, customers, products or any Sanko figures. Delegates the work to the Sanko MCP connector, which computes on the Sanko server and returns files. Role: Kinh doanh.
---

# Sanko connector (Kinh doanh)

Sanko runs an agent on the company server, reachable through the **Sanko MCP** connector. The server holds Sanko's data and does the querying and calculation; it returns results as files.

## When to use

Use the Sanko connector whenever the user asks about Sanko's own business: Sanko sales, revenue, orders, customers, products or any Sanko figures. Examples: "doanh thu tháng này", "báo cáo công nợ", "phân tích traffic website", "làm file Excel tổng hợp".

The people using this skill work at Sanko: a question about "công ty mình", "doanh thu", "báo cáo" and the like is about Sanko unless another company is named. Send it to Sanko without first asking whether it is about Sanko.

Never estimate, guess or invent Sanko figures, and never build them yourself in the chat or in a file, even when the user asks for an estimate, an industry average or a "rough number". Send the request to Sanko first. If Sanko reports missing data, offer a sample-data file made by Sanko instead: submit a task asking for sample data labelled "DỮ LIỆU MẪU" with its assumptions listed. If the connector is unavailable, say so and ask the user to connect **Sanko MCP** in Claude Desktop (Settings → Connectors).

## Scope for this role

Ask Sanko to query and calculate sales figures on request; answers come back as short results and files. Salary, receivables/payables and cost of goods are not available to this role.

Other installed skills (data analysis, marketing, finance) help you frame the request and interpret the result. They do not replace Sanko: company data lives on the Sanko server and all querying and computation happens there.

## How to work with Sanko

1. Call `agent_profile` once per conversation to see the role and available server skills.
2. Call `submit_task` with clear Vietnamese instructions and a new, stable `request_id` (any short unique text such as `kqkd-q3-1`; reuse it only to retry the same request). Say which file format is wanted when it matters (Excel, Word, PDF, PNG chart).
3. Every task response carries `estimated_remaining_seconds` and `check_again_after_seconds`. Tell the user roughly when the result will be ready (for example "khoảng 2 phút nữa"), then call `get_task` with `wait_seconds` set to `check_again_after_seconds`; it long-polls and returns early when the task finishes. Do not call it rapidly.
4. If the task returns `pending_question`, ask the user and answer with `reply_task` (stable `answer_id`).
5. For follow-ups on the same work, use `continue_task` with the latest finished task.

## Delivering results

- When the task succeeds, call `list_artifacts` and give the user the download links. Links expire after fifteen minutes; call `list_artifacts` again for fresh links.
- Do not open, read or paste file contents into the chat unless the user explicitly asks. This keeps company data on the server and saves tokens.
- Relay the task's short summary and headline figures; do not recompute or embellish them.
- Always pass on the data sources (schema.table and period) and the skills Sanko reports. If files or the summary are labelled "DỮ LIỆU MẪU", say clearly that the figures are sample data, not real Sanko data.
- If Sanko returns `blocked` or reports missing data, tell the user what is missing; never fill the gap with your own estimates or general knowledge.
