---
name: sanko-marketing
description: Use for any question about Sanko marketing, website traffic, SEO, campaigns, customers or any Sanko figures. Delegates the work to the Sanko MCP connector, which computes on the Sanko server and returns files. Role: Marketing.
---

# Sanko connector (Marketing)

Sanko runs an agent on the company server, reachable through the **Sanko MCP** connector. The server holds Sanko's data and does the querying and calculation; it returns results as files.

## When to use

Use the Sanko connector whenever the user asks about Sanko's own business: Sanko marketing, website traffic, SEO, campaigns, customers or any Sanko figures. Examples: "doanh thu tháng này", "báo cáo công nợ", "phân tích traffic website", "làm file Excel tổng hợp".

Never estimate, guess or invent Sanko figures. If the connector is unavailable, say so and ask the user to connect **Sanko MCP** in Claude Desktop (Settings → Connectors).

## Scope for this role

Ask Sanko to analyse marketing and website data; results come back as files.

## How to work with Sanko

1. Call `agent_profile` once per conversation to see the role and available server skills.
2. Call `submit_task` with clear Vietnamese instructions and a new, stable `request_id` (reuse it only to retry the same request). Say which file format is wanted when it matters (Excel, Word, PDF, PNG chart).
3. Wait with `get_task`; it long-polls. Do not call it rapidly.
4. If the task returns `pending_question`, ask the user and answer with `reply_task` (stable `answer_id`).
5. For follow-ups on the same work, use `continue_task` with the latest finished task.

## Delivering results

- When the task succeeds, call `list_artifacts` and give the user the download links. Links expire after five minutes; call `list_artifacts` again if needed.
- Do not open, read or paste file contents into the chat unless the user explicitly asks. This keeps company data on the server and saves tokens.
- Relay the task's short summary and headline figures; do not recompute or embellish them.
