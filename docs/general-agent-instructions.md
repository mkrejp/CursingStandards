# Agents instructions — general backend project

Standing brief for a **fresh agent context**. Prefer this file + linked gate docs over chat history.
Never invent or paste secrets (`DATABASE_URL`, tokens, PATs).

**Filename note:** use `general-agent-instructions.md` (correct spelling: *instructions*). Prefer this over any typo variant.

**Operating flow:** **5 gates** — Plan → Lock → Scaffold → Build → Ship (see gates section). Old stages 1–9 are a short legacy map only.

Placeholders used below (fill from project prefs / Context store):

| Placeholder | Meaning |
| --- | --- |
| `<PROJECT_NAME>` | Human project label |
| `<DEV_REPO>` | Private Origin/Codebase repo (source of truth for PRs) |
| `<RELEASE_MIRROR>` | Public/private GitHub (or similar) deploy mirror |
| `<HOST_PROJECT>` / `<HOST_SERVICE>` | App host project + service (e.g. Railway) |
| `<DB_PROJECT>` | Managed Postgres project (e.g. Supabase) |
| `<PUBLIC_HOSTNAME>` | Custom public API hostname |
| `<LOCAL_DEV_PATH>` | Preferred trusted-network checkout (e.g. WSL path) |
| `<CONTEXT_STORE>` | Cursor Project store path (often `/cursor/stores/self/`) |
| `<GEOJSON_API_BASE_URL>` | External GeoJSON/feature API base (optional seed) |
| `<LAYER_OR_COLLECTION_PATH>` | Layer, collection, or query path under that base |
| `<SOURCE_OBJECT_ID_FIELD>` | Stable feature id property used for upsert |

```
╔══════════════════════════════════════════════════════════════════╗
║  REPOS · HOST · DATABASE  (topology)                             ║
╚══════════════════════════════════════════════════════════════════╝
    Topology: private Codebase → optional release mirror → host + Postgres.
```

Dev lands on a private Origin-style Codebase; a human (or explicit process) mirrors to a GitHub-style release repo; the host builds from the mirror. Managed Postgres stays off the app host’s own DB addon when free-tier credit matters.

| Role | Location | Notes |
| --- | --- | --- |
| **Dev / PRs** | Origin/Codebase `<DEV_REPO>` | Source of truth; draft PRs here |
| **Release mirror** | GitHub (or equiv.) `<RELEASE_MIRROR>` (`main`) | Deploy source; prefer **manual mirror from trusted machine / WSL** |
| **Host** | e.g. Railway `<HOST_PROJECT>` / `<HOST_SERVICE>` | Autodeploy from `<RELEASE_MIRROR>` `main` |
| **DB** | e.g. Supabase `<DB_PROJECT>` (Free by default) | **Pooler** URI → host `DATABASE_URL` |
| **Public API** | `https://<PUBLIC_HOSTNAME>` | CNAME → host; health `/health` |
| **Trusted local** | `<LOCAL_DEV_PATH>` | Prefer for external sync if cloud IPs are WAF-blocked |

```
  Origin <DEV_REPO>  --(human WSL/manual mirror)-->  GitHub <RELEASE_MIRROR>
                                                              |
                                                              v
                                                   Host <HOST_SERVICE>
                                                              |
                                    DATABASE_URL (pooler)     v
                                                   Postgres <DB_PROJECT>
```

**Agents do not push the release mirror** unless the user explicitly grants that. Ask immediately if Codebase write, host, or `DATABASE_URL` access is missing.

Project Context store: `<CONTEXT_STORE>` (symlink may alias a `bc-…` id — same tree).

---

```
╔══════════════════════════════════════════════════════════════════╗
║  MISSION & WORKING STYLE                                         ║
╚══════════════════════════════════════════════════════════════════╝
    Speed over ceremony; ask only blockers; free-tier by default.
```

Build a small **HTTP + Postgres** backend through the **5 gates**. Stay **free-tier by default** (paid only as escape hatch).

1. Ask only questions needed for an optimal solution; **speed**, no overthinking.
2. Missing CLI/SSH/API credentials **or an unauthenticated required plugin** that should already exist → **ask the user immediately**; do not spin.
3. Never commit secrets; `.env.example` without values only.
4. Keep changes minimal and gate-appropriate; do not reopen locked stack/domain choices without confirmation.
5. Prefer **Context (Agent Store)** + installed MCP/plugins over burying facts in chat or reinventing ops APIs.

Standing prefs: `<CONTEXT_STORE>/preferences.md` · overview docs in `<CONTEXT_STORE>/docs/`.

---

```
╔══════════════════════════════════════════════════════════════════╗
║  CONTEXT STORE · MCP / PLUGINS                                   ║
╚══════════════════════════════════════════════════════════════════╝
    Lasting docs in Context; use installed tools — don’t reinvent them.
```

**Context store = living source of truth (SoT)** for ops (topology, locks, tip, do/don’t, prefs, gate status). Chat is ephemeral; store files are the handoff. Update Context first; dual-edit Context + repo in the same turn only when the task is explicitly “sync the brief.”

**Context (Agent Store) — write lasting work here**

| Path | Use for |
| --- | --- |
| `<CONTEXT_STORE>/docs/` | Standing briefs, gate plans, runbooks, design artifacts |
| `<CONTEXT_STORE>/docs/agent-bootstrap.md` | Default fresh-agent entry (~80–120 lines) |
| `<CONTEXT_STORE>/notes.md` | Short current status / tip for the next agent |
| `<CONTEXT_STORE>/preferences.md` | Reusable user prefs (stack locks, free-tier bias, mirror rules) |
| `<CONTEXT_STORE>/internal/` | Working notes / evidence not meant as primary user docs (when needed) |

- Prefer updating these over restating the same topology in every reply.
- `self` may symlink to a `bc-…` store id — **same tree**; no duplicate copy needed.
- Repo `docs/` gets in-repo copies when the deliverable ships with the code (draft Codebase/Origin PR).
- **Batch Origin PRs** — one draft PR per coherent package/gate; land related merges together.
- **Batch remirrors** — human remirrors after deployable batches, not after every edit.
- **Skip remirror / host rebuild** when changes are Context-only or docs that do **not** affect the deploy image (`src/`, `migrations/`, `Dockerfile`, package lock, image sync scripts, env contract).

**Installed MCP / plugins — use when relevant**

Plugins and MCP servers are already part of the agent environment when installed. Discover tool schemas **before** calling; authenticate **only when needed** (e.g. after a 401 / `needsAuth`), not preemptively for every namespace.

| Area (examples) | Prefer plugin/MCP for | Don’t reinvent |
| --- | --- | --- |
| **Managed Postgres** (e.g. Supabase) | List projects/tables, migrations, advisors, SQL, logs, project URL/keys | Hand-rolled dashboard scraping |
| **App host** (e.g. Railway) | Services, vars, deploys, logs, metrics, domains | Guessing deploy state from memory |
| **Code forge** (Origin / GitHub as configured) | PR create/view/checks/comments, commits | Blind `git` + guessing CI |
| **Cursor cloud / Project tools** | Run info, agent lists, subscriptions wait (CI/PR), environment helpers | Busy-polling when subscribe tools exist |
| **Docs / live web** (when installed) | Library docs, brand/scrape/search skills | Outdated training guesses for APIs |

**Rules**

1. If a task needs DB schema, deploy status, PR state, or host vars — **call the matching installed tool** after schema discovery.
2. Do not re-implement list-tables / apply-migration / deploy-status / PR ops in ad-hoc scripts when the plugin already does it.
3. Missing or unauthenticated required plugin → **ask the user immediately** (same as credential-blocker rule). Do not invent workarounds that need secrets you don’t have.
4. Never paste secrets returned by tools into docs or chat; reference *where* they live (host vars, DB pooler UI).

**Ask user only if needed:** which plugins are in scope for this machine? auth blocked on a required MCP?

---

```
╔══════════════════════════════════════════════════════════════════╗
║  LOCKED STACK  (typical defaults — confirm per project)          ║
╚══════════════════════════════════════════════════════════════════╝
    Confirm o