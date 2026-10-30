# Desktop Cursor recovery

Public, redacted restore guide for a clean Cursor desktop install (account `mkrejp`).

Snapshot: **2026-10-30** (plugins/MCP observed from a Cursor cloud session). Desktop `settings.json` / CLI / extensions last captured **2026-10-02** on Windows — re-check those files on the new machine if they differ.

Do **not** paste OAuth tokens, API keys, GitHub PATs, or live MCP URLs with secrets into this repo or into chat history you will commit. Re-authenticate each service on the new install.

Companion inventory: [desktop-cursor-recovery.reference.md](desktop-cursor-recovery.reference.md).

Agent docs index: [docs/INDEX.md](docs/INDEX.md). Keep session context small ([docs/agent-context-budget.md](docs/agent-context-budget.md)); write hierarchical handoffs ([docs/agent-structured-docs.md](docs/agent-structured-docs.md)). PostgreSQL: [docs/postgresql.md](docs/postgresql.md).

---

## Goal checklist

1. Install Cursor; sign in with the same account.
2. Restore **settings** + note **CLI** preferences (below).
3. Install **VS Code/Cursor extensions**.
4. Install **marketplace plugins** and complete each auth.
5. Optionally restore **user-level** `mcp.json` for QGIS (and project-scoped Supabase if you still use it).
6. Restore **user rules**.
7. Ensure system deps as needed: `uv`/`uvx`, WSL Ubuntu, Node (`npx` for Canva MCP), QGIS only if using QGIS MCP, `gh`, Git.
8. Open a home window for general work; move into a real project root before code changes.

---

## 1. Settings (`%APPDATA%\\Cursor\\User\\settings.json`)

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

Prefer Cursor’s update-settings skill when editing settings on an existing install.

---

## 2. Cursor CLI config (`%USERPROFILE%\\.cursor\\cli-config.json`)

Key preferences last observed:

| Key | Value |
|-----|--------|
| `display.mode` | `zen` |
| `approvalMode` | `allowlist` |
| `sandbox.mode` | `disabled` |
| `steering` / `rewind` / `hints` | `true` |
| `attribution.attributeCommitsToAgent` | `true` |
| `attribution.attributePRsToAgent` | `true` |
| `permissions.allow` | includes `Shell(ls)` |

Do not invent new allowlist entries without asking the user.

---

## 3. Extensions

| Extension ID | Purpose |
|--------------|---------|
| `anysphere.remote-ssh` | Remote SSH |
| `anysphere.remote-wsl` | Remote WSL |
| `github.vscode-github-actions` | GitHub Actions |
| `github.vscode-pull-request-github` | PRs in editor |

WSL distro in use historically: **Ubuntu** (WSL2).

---

## 4. Marketplace plugins

Install via the Cursor plugin marketplace. After install, **authenticate each MCP** (`mcp_auth` or product OAuth) before relying on tools.

| Plugin slug | Role | Auth |
|-------------|------|------|
| `supabase` | Supabase projects + skills | Plugin OAuth / `mcp_auth` |
| `railway` | Deploy/manage Railway | Railway login / MCP auth |
| `canva` | Design create/edit/brand skills | Canva OAuth; needs Node/`npx` |
| `gmail` | Mail search/draft/send | Google OAuth |
| `google-slides` | Decks via MCP | Google OAuth (Cursor ≥ 3.22) |
| `github` | Issues, PRs, Actions, repos | GitHub OAuth and/or a **new** PAT in plugin config — never commit the PAT |
| `context-dev` | Live web search/scrape/crawl/brand/monitors | Context OAuth or API key |
| `ahrefs` | SEO / site explorer | Ahrefs API auth (Cursor ≥ 3.13) |
| `context7-plugin` (or Settings → Context7) | Live library docs | `mcp_auth` if prompted |

**Agent rule:** Prefer Context7 for library/framework docs; prefer Context.dev for live web/scrape/brand when that plugin is present.

Plugin MCP base URLs (no secrets): see [reference](desktop-cursor-recovery.reference.md).

---

## 5. Optional user-level MCP (`%USERPROFILE%\\.cursor\\mcp.json`)

Plugins register their own servers. User-level `mcp.json` is only for extras such as QGIS.

### Optional: project-scoped Supabase (HTTP)

If you still use a project-specific Supabase MCP URL (in addition to the plugin):

1. Open the Supabase dashboard MCP setup for the project.
2. Paste the generated URL into `mcp.json`.
3. Do not commit the live URL or `project_ref` into git.

Template (placeholders only):

```json
{
  "mcpServers": {
    "supabase": {
      "url": "https://mcp.supabase.com/mcp?project_ref=YOUR_PROJECT_REF&features=docs%2Caccount%2Cdatabase%2Cdebugging%2Cdevelopment%2Cfunctions%2Cbranching"
    }
  }
}
```

### Optional: qgis (stdio via uvx)

Requires `uv`/`uvx` on PATH and QGIS running with the MCP bridge.

```json
{
  "mcpServers": {
    "qgis": {
      "command": "uvx",
      "args": [
        "--from",
        "https://github.com/nkarasiak/qgis-mcp/archive/refs/heads/main.zip",
        "qgis-mcp-server"
      ]
    }
  }
}
```

---

## 6. User rules to restore

Recreate these as Cursor user rules (Settings → Rules, and/or `%USERPROFILE%\\.cursor\\rules\\`):

1. **Web-app verification** — verify UI changes in the browser before calling frontend work done.
2. **Affirmative wording** — prefer saying what is true; avoid contrastive “not X, but Y” phrasing.
3. **Environment restore pointer** — for clean installs / reconnecting plugins, follow this repo’s `desktop-cursor-recovery.md` (and optionally a local copy under `%USERPROFILE%\\.cursor\\skills\\cursor-environment-setup\\`).
4. **Python** — typing, pytest, uv, Ruff, PEP 257.
5. **PostgreSQL** — restore from [docs/postgresql.md](docs/postgresql.md) (TIMESTAMPTZ, parameterized SQL, migrations, indexing).

Do not store secrets in rule bodies.

---

## 7. Workspace conventions

| Topic | Convention |
|-------|------------|
| Home / empty window | General tasks only; not a project root |
| Project work | Move into a real project directory before edits/installs/commits |
| Preferred parents | `~/Projects` or `~/Developer` if created; else home or `Documents\\GitHub` |
| Public repos (examples) | `MCP-inspectoriat`, `FastMCP-testserver`, `MCP-uni-hubs`, `CursingStandards`, `sbdb`, `tildb` under GitHub user `mkrejp` |
| Private repos | Clone only the private repos you already use, after `gh auth login` — do not list them in public docs |
| New projects | Confirm name → create dir + git → move agent into that root → scaffold |
| Built-in skills | `%USERPROFILE%\\.cursor\\skills-cursor\\` (Cursor-managed; do not edit) |
| Personal skills | `%USERPROFILE%\\.cursor\\skills\\` |

This public guide is the durable restore source of truth. A thin local skill may point here after install.

---

## 8. Ordered restore procedure

1. Confirm path layout (`%USERPROFILE%\\.cursor`, `%APPDATA%\\Cursor\\User`).
2. Apply `settings.json` and note CLI preferences.
3. Install the four extensions above.
4. Install each plugin in §4; authenticate MCP for each.
5. If needed: write optional `mcp.json` for QGIS and/or project Supabase (user supplies refs from dashboards).
6. Verify `uvx` if using QGIS; start QGIS before calling that MCP.
7. List user rules; add the five in §6 if absent.
8. Offer to create `~/Projects` for future roots.
9. Smoke-test (see §9).

Never paste API keys into this guide or into git.

---

## 9. Smoke tests

| Integration | Quick check |
|-------------|-------------|
| Context7 | Resolve a known library → query docs |
| Context.dev | `web-search` (or connect skill if unauthorized) |
| Supabase | Auth, then list projects / docs search |
| GitHub | `get_me` → expect login `mkrejp` |
| Railway | `whoami` / `list-projects` after login |
| QGIS | Only if installed; ping / list instances with QGIS open |
| Canva / Gmail / Slides | List designs / labels / presentations after OAuth |
| Ahrefs | Subscription/limits or docs tool after auth |
