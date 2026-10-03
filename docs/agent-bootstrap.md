# Agent bootstrap — Sample DB backend

**Default fresh-agent entry** (~100 lines). Read this + `notes.md` first. Deeper docs only when the gate needs them.

**Do not** reload full `general-agent-instructions.md` or stage 1–6 archives on routine **Build / Ship** work.

```
╔══════════════════════════════════════════════════════════════════╗
║  FIVE GATES  (living flow)                                       ║
╚══════════════════════════════════════════════════════════════════╝
```

**Plan → Lock → Scaffold → Build → Ship**

| Gate | One-liner | Open when |
| --- | --- | --- |
| **Plan** | Architecture + domain outline | New project / scope rethink |
| **Lock** | Stack, host, DB, DNS, repos, free-tier, domain | Before Scaffold; stop for Marek |
| **Scaffold** | Framework, migrations design, CI stubs, design docs | New repo slice |
| **Build** | APIs, sync code, PostGIS/columns, tests | Implementing packages |
| **Ship** | Deploy, remirror, smoke, version, costs if asked | Runtime/image change |

Legacy 1–9 → gates: 1→Plan · 2–3→Lock · 4→Scaffold · 5–6+impl→Build · 7–9→Ship. Archives: unread unless reopening a decision.

```
╔══════════════════════════════════════════════════════════════════╗
║  TOPOLOGY                                                        ║
╚══════════════════════════════════════════════════════════════════╝
```

| Role | Where |
| --- | --- |
| Dev / PRs | Origin `marek-k-ejpsk/genesis` |
| Release mirror | GitHub `mkrejp/sbdb` — **Marek WSL only** |
| Host | Railway **zesty-adaptation** / **sbdb-api** |
| DB | Supabase **samplebackdb** (pooler → `DATABASE_URL`) |
| Public API | https://sbdb.animarium.ai · `/health` |
| Local | `/home/cursor/dev/genesis` |

Agents **never** push GitHub. Ask immediately if Origin write, Railway, or `DATABASE_URL` missing.

```
╔══════════════════════════════════════════════════════════════════╗
║  LOCKS (do not reopen)                                           ║
╚══════════════════════════════════════════════════════════════════╝
```

Python 3.12+ · FastAPI · psycopg 3 · SQL migrations/`psql -f` (**no Alembic**) · Ruff/pytest · Railway (no host Postgres) · Supabase Free · validation **422** · out of scope: auth/queues/UI/Redis/paid APM.

```
╔══════════════════════════════════════════════════════════════════╗
║  CONTEXT = SOURCE OF TRUTH                                       ║
╚══════════════════════════════════════════════════════════════════╝
```

- **Living ops** (topology, locks, tip, do/don’t, prefs) live in **Context** (`/cursor/stores/self/`). Chat is ephemeral.
- Update Context first; repo `docs/` only when the deliverable ships with code.
- Prefer one Origin draft PR per coherent package/gate — **batch** related merges.
- Marek **remirrors in batches** after deployable merges — not after every doc edit.
- **Skip GitHub remirror / Railway rebuild** when changes are Context-only or docs that do **not** affect the deploy image (`src/`, `migrations/`, `Dockerfile`, `pyproject.toml`/lock, image sync scripts, env contract).

```
╔══════════════════════════════════════════════════════════════════╗
║  SYNC = WSL ONLY                                                 ║
╚══════════════════════════════════════════════════════════════════╝
```

Live GeoJSON / **NPÚ** sync runs from **WSL / trusted network only**. Cloud IPs are WAF-blocked — **never** assign live sync to cloud agents. Mock provider in CI. Detail: `docs/npu-sync-wsl.md`.

```
╔══════════════════════════════════════════════════════════════════╗
║  ORCHESTRATION  (fewer, longer agents)                           ║
╚══════════════════════════════════════════════════════════════════╝
```

Prefer one long turn per gate/package; cap **≤2** concurrent workers. **Don’t fan out** for curls, remirror watches, docs-only, or live GeoJSON/NPÚ sync. Detail: `docs/general-agent-instructions.md` § AGENT ORCHESTRATION.

```
╔══════════════════════════════════════════════════════════════════╗
║  TIP · DO / DON’T · LOAD ORDER                                   ║
╚══════════════════════════════════════════════════════════════════╝
```

**Tip:** Live API **0.2.0** · gates past Scaffold/Build for core slice · Ship for remirror/smoke/version · Stage-9 cost only if Marek asks · status: `notes.md`.

**Do:** free-tier · parameterized SQL · versioned migrations · draft Origin PRs · ask on credential blockers · WSL for NPÚ · Context SoT · 5 gates.

**Don’t:** push `mkrejp/sbdb` · paste secrets · cloud live sync · reopen locks · invent Cursor $ · remirror for Context-only · fan out for curls/remirror watches · reload general-agent-instructions / stages 1–6 on routine Build/Ship.

**Load order:** (1) `notes.md` (2) this bootstrap (3) `preferences.md` if locks unclear (4) **one** deep doc for the gate/task (5) live MCP/code as needed.

| Need | Open |
| --- | --- |
| Full project brief | `docs/agents-instructions.md` |
| New-project template / gate detail | `docs/general-agent-instructions.md` |
| Cheaper-ops rationale | `docs/flow-simplification-recommendations.md` |
| Prefs | `preferences.md` |
| NPÚ WSL | `docs/npu-sync-wsl.md` |
| Costs | `docs/stage-nine-costs.md` (Ship, if asked) |
