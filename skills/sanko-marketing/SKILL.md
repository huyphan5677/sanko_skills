---
name: sanko-marketing
description: Use for any question about Sanko marketing, website traffic, SEO, campaigns, customers or any Sanko figures. Also use it for work files built from Sanko data. Delegates the work to the Sanko MCP connector, which computes on the Sanko server and returns files. Role: Marketing.
---

# Sanko connector (Marketing)

Sanko runs an agent on the company server, reachable through the **Sanko MCP** connector. The server holds Sanko's data and does the querying and calculation; it returns results as files.

## When to use

Use the Sanko connector whenever the user asks about Sanko's own business: Sanko marketing, website traffic, SEO, campaigns, customers or any Sanko figures. Examples: "doanh thu tháng này", "báo cáo công nợ", "phân tích traffic website", "làm file Excel tổng hợp".

The people using this skill work at Sanko: a question about "công ty mình", "doanh thu", "báo cáo" and the like is about Sanko unless another company is named. Send it to Sanko without first asking whether it is about Sanko.

Never estimate, guess or invent Sanko figures, and never build them yourself in the chat or in a file, even when the user asks for an estimate, an industry average or a "rough number". Send the request to Sanko first. If Sanko reports missing data, offer a sample-data file made by Sanko instead: submit a task asking for sample data labelled "DỮ LIỆU MẪU" with its assumptions listed. If the connector is unavailable, say so and ask the user to connect **Sanko MCP** in Claude Desktop (Settings → Connectors).

## Scope for this role

Send to Sanko: which advertising channel performed best (cost per lead, cost per order, budget suggestion); marketing performance over recent months; analysis of an uploaded file combined with Sanko data.

Do yourself, with your own skills and web access: posts and content in the Sanko voice, SEO audits of the website, competitor research. The Sanko server has no Internet access for this role.

Anything else is outside what Sanko serves for this role: Sanko answers `blocked` for it. Do not work around that; tell the user it is not served. `agent_profile` lists the served use cases (`primary_use_cases`) and the ones you do yourself (`client_side_use_cases`).

Other installed skills (data analysis, marketing, finance) help you frame the request and interpret the result. They do not replace Sanko: company data lives on the Sanko server and all querying and computation happens there.

## How to work with Sanko

1. Call `agent_profile` once per conversation to see the role and available server skills.
2. Call `submit_task` with clear Vietnamese instructions and a new, stable `request_id` (any short unique text such as `kqkd-q3-1`; reuse it only to retry the same request). Say which file format is wanted when it matters (Excel, Word, PDF, PNG chart). Files go to Sanko only as files: when the user gives a file, call `request_upload` with its file name, then run the returned `command` in your shell or code tool with `FILE` replaced by the file's local path. The file's contents reach Sanko through that upload, not through the instructions. Pass the returned `input_id` in `input_ids`. Never paste file contents or data rows into the instructions; if the user pasted rows into the chat, ask them to attach the file. Sanko already knows who is asking from the signed-in account: "của mình", "của tôi" mean that account's own figures. Never add the user's email, name or other personal details to the instructions.
3. Every task response carries `estimated_remaining_seconds` and `check_again_after_seconds`. Tell the user roughly when the result will be ready (for example "khoảng 2 phút nữa"), then call `get_task` with `wait_seconds` set to `check_again_after_seconds`; it long-polls and returns early when the task finishes. Do not call it rapidly.
4. If the task returns `pending_question`, ask the user and answer with `reply_task` (stable `answer_id`).
5. For follow-ups on the same work, use `continue_task` with the latest finished task.

## Delivering results

- When the task succeeds, call `list_artifacts` and give the user the download links. Links expire after fifteen minutes; call `list_artifacts` again for fresh links.
- Do not open, read or paste file contents into the chat unless the user explicitly asks. This keeps company data on the server and saves tokens.
- Relay the task's short summary and headline figures; do not recompute or embellish them.
- Copy figures, periods and labels from Sanko's summary exactly as written. Never round, recompute, re-label or reinterpret them, and never add figures of your own. Always pass on the data sources (schema.table and period) and the skills Sanko reports.
- Labels mean exactly this. "DỮ LIỆU GIẢ LẬP": the company data of the current development environment, computed normally; say "số liệu từ dữ liệu giả lập của môi trường phát triển" and nothing more (it does not mean real data is missing or not ready). "DỮ LIỆU MẪU": figures Sanko made up because the user asked for sample data. Keep the label in every answer that carries the figures.
- If Sanko says a figure could not be verified ("Chưa kiểm chứng tự động được"), pass that on unchanged.
- If Sanko returns `blocked` or reports missing data, tell the user what is missing; never fill the gap with your own estimates or general knowledge.
