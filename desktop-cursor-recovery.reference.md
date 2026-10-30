# Desktop Cursor recovery — reference inventory

Redacted companion to [desktop-cursor-recovery.md](desktop-cursor-recovery.md).  
Snapshot: **2026-10-30** (plugins/MCP from Cursor cloud). Desktop settings/CLI/extensions last captured **2026-10-02**.

No secrets, no private repo lists, no machine-specific IDs, no absolute username paths.

## Paths (Windows desktop)

| Item | Path |
|------|------|
| Cursor user data | `%APPDATA%\Cursor\User\` |
| settings.json | `%APPDATA%\Cursor\User\settings.json` |
| Cursor home | `%USERPROFILE%\.cursor\` |
| MCP config | `%USERPROFILE%\.cursor\mcp.json` |
| CLI config | `%USERPROFILE%\.cursor\cli-config.json` |
| User rules (files) | `%USERPROFILE%\.cursor\rules\` |
| Marketplace plugins cache | `%USERPROFILE%\.cursor\plugins\cache\cursor-public\` |
| Extensions | `%USERPROFILE%\.cursor\extensions\` |
| Built-in skills | `%USERPROFILE%\.cursor\skills-cursor\` |
| Personal skills | `%USERPROFILE%\.cursor\skills\` |

## Plugin MCP endpoints (public bases only)

| Plugin | Transport | URL / command |
|--------|-----------|---------------|
| supabase | HTTP | `https://mcp.supabase.com/mcp` |
| railway | HTTP | `https://mcp.railway.com` |
| canva | stdio | `npx -y mcp-remote@latest https://mcp.canva.com/mcp` |
| gmail | HTTP | `https://gmailmcp.googleapis.com/mcp/v1` |
| google-slides | HTTP | `https://api.cursor.com/rest-mcp/google-slides/mcp` |
| github | HTTP | `https://api.githubcopilot.com/mcp/` with `Authorization: Bearer ${GITHUB_PERSONAL_ACCESS_TOKEN}` (set locally; never commit) |
| context-dev | HTTP | `https://mcp.context.dev/mcp` |
| ahrefs | HTTP | `https://api.ahrefs.com/mcp/mcp` |
| context7 | (plugin) | Authenticate via Cursor; use `resolve-library-id` / `query-docs` |

## Plugins observed installed (user marketplace)

| Name | Notes |
|------|-------|
| supabase | Skills: supabase, supabase-postgres-best-practices |
| railway | Skill: use-railway |
| canva | Skills: brand-check, bulk-create, cli, edit-design, get-design-feedback, implement-feedback, resize-for-social-media |
| gmail | MCP only |
| google-slides | Skill: google-slides |
| github | MCP only; PAT via plugin variables |
| context-dev | Skills + rules for prefer-MCP / API security |
| ahrefs | MCP only |

Context7 may appear as a connected MCP without a matching entry in the user plugin cache — still install/auth if missing.

## Optional user MCP: QGIS

- Transport: stdio via `uvx`
- Package: `https://github.com/nkarasiak/qgis-mcp/archive/refs/heads/main.zip` → `qgis-mcp-server`
- Runtime: `uv`/`uvx` on PATH; QGIS app with MCP bridge

## Extensions (versions last seen 2026-10-02)

- `anysphere.remote-ssh`
- `anysphere.remote-wsl`
- `github.vscode-github-actions`
- `github.vscode-pull-request-github`

Pin exact versions only if the new machine needs them; marketplace current is fine.

## Public GitHub repos (`mkrejp`)

Examples of public work (not a clone-everything list):

- `MCP-inspectoriat`
- `FastMCP-testserver`
- `MCP-uni-hubs`
- `CursingStandards` (this guide)
- `sbdb`
- `tildb`

Private repositories: authenticate with GitHub, then clone what you already use. Do not publish private names here.

## Railway

After Railway MCP auth, run `list-projects` / `whoami`. Expect projects in your Railway workspace. Do not store project UUIDs in this repo.

## settings.json (full, non-secret)

```json
{
  "window.autoDetectColorScheme": true,
  "cursor.composer.queueMessageDefaultBehavior": "steer",
  "workbench.colorTheme": "Cursor Light",
  "cursor.composer.conversationDensity": "detailed",
  "cursor.composer.codeBlockWordWrap": true,
  "cursor.general.reduceTransparency": true
}
```

## cli-config.json (notable fields)

- `display.mode`: `zen`
- `approvalMode`: `allowlist`
- `sandbox.mode`: `disabled`
- `sandbox.networkAccess`: `user_config_with_defaults`
- `steering`, `rewind`, `hints`, `notifications`, `modelSlashCommands`: true
- `autoAcceptWebSearch`: false
- `attribution.attributeCommitsToAgent`: true
- `attribution.attributePRsToAgent`: true
- `permissions.allow`: `["Shell(ls)"]`
- `exploreSubagentModel`: `default`

## System prerequisites

- **uv / uvx** — for optional QGIS MCP
- **WSL2 Ubuntu** — if using Remote WSL
- **Node / npx** — for Canva MCP remote
- **QGIS** — only if using QGIS MCP
- **gh** — GitHub CLI for PR workflows
- **Git** — expected

## What never belongs in this public repo

- OAuth tokens, API keys, GitHub PATs, session cookies
- Live Supabase `project_ref` or full authenticated MCP URLs
- Railway project UUIDs
- Cloud environment / build public IDs
- Personal agent-store user IDs or absolute username paths
- Private repository names or descriptions
- Personal email addresses
- `argv.json` crash-reporter / machine IDs
- Full live `mcp.json` copied from a machine
