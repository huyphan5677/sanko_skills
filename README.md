# Sanko skills

Skills for Sanko staff using Claude Desktop with the **Sanko MCP** connector, and for the Sanko server agent.

Everything is declared in [`skills.json`](skills.json):

- `sources`: GitHub repositories, each pinned to a commit or tag, and the folder that holds its skills.
- `groups`: named lists of skills written as `<source>/<skill>`; `<source>/*` means every skill folder in that source.
- `roles`: for each role (`admin`, `accounting`, `marketing`, `sales`), the groups installed in Claude (`client`) and on the Sanko server (`server`).

This repository is public. It must never contain passwords, tokens or company data.

## Install for Claude (instructions for Claude)

The user gives you this repository URL and their role. Perform a **clean install** so every run leaves exactly the role's skills. Work in this order and do not skip steps.

1. **Resolve.** Read `skills.json` at the default branch. Take `roles.<role>.client`, expand every group to `<source>/<skill>` entries, and expand `*` by listing the skill folders (folders containing `SKILL.md`) under that source's `path` at its `ref`. The result is the **role set**. The **managed set** is the same expansion over every group in `skills.json`, for all roles.
2. **List.** List the skills currently installed for this user (the local skills directory, for example `~/.claude/skills/`). Show the list.
3. **Remove.** Delete every installed skill whose name is in the managed set, including skills of other roles and older copies of this role's skills. Leave any other skill untouched and mention it.
4. **Install.** For each entry in the role set, download the folder `<path>/<skill>` from the source `repo` at its exact `ref` (commit or tag, never a branch) and install it under its folder name. Install the whole folder, not only `SKILL.md`.
5. **Report.** Report the role, what was removed, what was installed with its source and ref, and anything that failed.

If you cannot write to the skills directory, still perform steps 1, 2 and 5, package each skill in the role set as a `.zip` of its folder, and tell the user to remove the listed old skills and upload the zips under **Settings → Capabilities → Skills**.

## Roles

| Role | Claude skills | Server skills |
|---|---|---|
| admin | connector + data + marketing + accounting | data discovery + data + marketing + accounting |
| accounting | connector + data + accounting | data discovery + data + accounting |
| marketing | connector + data + marketing | data discovery + data + marketing |
| sales | connector + data | data discovery + data |

The Sanko connector skills (`skills/sanko-<role>`) make Claude delegate Sanko questions to Sanko MCP, which computes on the server and returns files. `skills/sanko-data-discovery` makes the server agent discover the live warehouse schema, because the warehouse changes continuously.

## Sources

| Source | Repository | Licence |
|---|---|---|
| sanko | this repository | — |
| marketingskills | coreyhaines31/marketingskills | MIT |
| finance-analyst | alirezarezvani/claude-skills (`finance/skills`) | MIT |
| anthropic-finance, anthropic-data | anthropics/knowledge-work-plugins | Apache-2.0 |

## Changing skills

1. Edit `skills.json` (or a skill under `skills/`) and pin any new source to a commit.
2. Commit, then create a new tag (`v3`, …) and set `sources.sanko.ref` to it in the same change.
3. Users re-run the install: paste this repository URL into Claude with their role.
4. The operator re-syncs the server: `Get-SkillSources.ps1` then `Sync-AgentSkills.ps1` in the Sanko MCP repository, then restarts the coordinator.
