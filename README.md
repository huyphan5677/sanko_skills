# Sanko skills

Public skills for Sanko staff.

- `client/sanko-<role>/`: Claude Desktop skill that makes Claude delegate Sanko questions to the Sanko MCP connector.
- `client/roles/<role>.md`: install list per role, read by the install prompt on `https://mcp.sanko.com.vn/access`. Add third-party skills here as `- name | https://github.com/owner/repo | path/to/skill | @tag-or-commit`.
- `server/groups.json`: which server (Codex) skills each role gets; applied by `ops/Sync-AgentSkills.ps1` in the Sanko MCP repo.

Nothing here may contain passwords, tokens or company data: the repository is public.

After changing a client skill, tag a new version (for example `v2`) and update the `@` ref in the role lists.
