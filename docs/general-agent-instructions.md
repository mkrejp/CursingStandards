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
    Confirm once (Lock gate); treat as locked until the user changes them.
```

| Lock | Typical value |
| --- | --- |
| App | Python 3.12+ · FastAPI · Pydantic v2 · uvicorn · `uv` · Ruff · pytest |
| DB | Postgres via managed project (e.g. Supabase `<DB_PROJECT>`) |
| Driver / migrations | **psycopg 3** · versioned SQL under `migrations/` via `psql -f` (**no Alembic** unless user locks otherwise) |
| CI | GitHub Actions on the **release mirror** (Ruff → pytest; stub empty `DATABASE_URL`) |
| Host | e.g. Railway Free/Trial → Hobby escape; **no** host-managed Postgres if DB is already on Supabase-like Free |
| Hostname | `<PUBLIC_HOSTNAME>` → host |
| Validation | FastAPI default **422** (or document `400` if chosen) |
| Out of scope (baseline) | Auth, queues, UI, Redis, paid APM — unless a gate unlocks them |

**Ask once (Plan / Lock / prefs) if unset:** app language/framework? DB host product? CI + deploy targets? free-tier hard constraint? public hostname needed?

---

```
╔══════════════════════════════════════════════════════════════════╗
║  DOMAIN · DATA MODEL  (choose; document; then lock)              ║
╚══════════════════════════════════════════════════════════════════╝
    Outline in Plan; lock in Lock; schema artifacts in Scaffold.
```

Domain is project-specific. Example pattern (GeoJSON map layers — *example only*, not required identity):

| Table (example) | Role |
| --- | --- |
| `map_layers` | Layer containers (`source_url`, `source_key`, …) |
| `layer_objects` | Features: geometry (JSONB and/or PostGIS) + source keys |
| `layer_object_properties` | Typed props: text / temporal / image / binary (Storage refs, not BYTEA) |
| `tags` + `layer_object_tags` | Classification (M2M) |
| `layer_object_urls` | Ordered URLs per object (1:N) |

**Ask when needed:** domain theme? must-have entities? geometry/PostGIS now or later? external seed source?

Migrations: numbered SQL files in apply order; never auto-reset a shared demo DB.

---

```
╔══════════════════════════════════════════════════════════════════╗
║  FIVE GATES  (primary operating flow)                            ║
╚══════════════════════════════════════════════════════════════════╝
    Plan → Lock → Scaffold → Build → Ship. One coherent agent per gate when possible.
```

| Gate | Purpose (short) | Merges legacy | Stop for user? |
| --- | --- | --- | --- |
| **Plan** | Architecture + domain outline | Old 1 (+ early domain Qs) | Soft — before Lock if scope unclear |
| **Lock** | Stack, host, DB, DNS, repos, free-tier, domain locks | Old 2 + 3 (+ prefs) | **Yes** — once |
| **Scaffold** | Repo framework, migration design, CI stubs, design docs | Old 4 | Soft — merge when green |
| **Build** | Implement APIs, sync, PostGIS/columns as needed, tests | Old 5 + 6 + implement | Yes before first deployable merge if scope large |
| **Ship** | Deploy, mirror, smoke, version, costs/monitoring | Old 7 + 8 + 9 | Remirror is human; cost note when asked |

**Thin bootstrap:** `<CONTEXT_STORE>/docs/agent-bootstrap.md` (~80–120 lines) is the **default fresh-agent entry**. Point to deeper docs only when the gate needs them. **Do not** reload this full file or stage 1–6 archives on routine **Build / Ship** work. This file remains the new-project template / gate detail reference.

---

```
╔══════════════════════════════════════════════════════════════════╗
║  AGENT ORCHESTRATION  (fewer, longer agents)                     ║
╚══════════════════════════════════════════════════════════════════╝
    Prefer long coherent turns; don’t fan out for curls, remirror watches,
    docs-only, or live GeoJSON / external sync.
```

### Prefer

- **One long coherent turn** per gate or per work package (context reuse beats cold starts).
- **Coordinator** owns: `notes.md`, fan-out decisions, user questions, merge order.
- **Worker** gets: bootstrap path + task card + acceptance — not this full template.

### When NOT to fan out

| Situation | Instead |
| --- | --- |
| Docs-only / cost note / preferences tweak | Single agent or coordinator edit |
| Version bump + changelog | One PR agent; human remirrors; **curl** smoke — no watcher agent |
| “Is the host up?” | Host MCP or one `curl` — not a cloud agent |
| Gate/plan doc that only restates locks | Fold into Lock/Plan doc; skip extra agent |
| Parallel gate work when N depends on N−1 | Serial in one thread |
| Live GeoJSON / external sync | Trusted network / WSL shell — **never** cloud fan-out |
| Release-mirror push | Human only — never spawn an agent to push `<RELEASE_MIRROR>` |

### When fan-out is OK

- Truly independent packages after Scaffold (or an approved Build plan) **and** each worker has a tiny task card.
- Cap: **≤2** concurrent workers unless the user asks for more.
- Coordinator reconciles; workers do not open sibling PRs for the same files.

Fixed overhead (bootstrap reload, tool discovery, PR chrome) often dominates. Halving agent starts usually beats micro-optimizing prompts. Rationale: `docs/flow-simplification-recommendations.md` § AGENT ORCHESTRATION.

---

```
╔══════════════════════════════════════════════════════════════════╗
║  GATE — PLAN                                                     ║
╚══════════════════════════════════════════════════════════════════╝
    Architecture / domain outline; questions only as needed. No scaffolding.
```

**Purpose:** Frame the first teachable slice — components, interactions, connections, and a domain sketch — without locking tooling or scaffolding code.

**Steps**

1. State purpose / in-scope / out-of-scope for the first slice.
2. List components (HTTP API, service layer, persistence, migrations, config, DB) and ownership boundaries.
3. Describe happy path, empty/404, validation, conflict, 500, and health interactions.
4. Draw connections: client → API → env → Postgres; migrations as a separate step.
5. Outline domain entities at a sketch level (tables/roles), not final SQL.
6. Draft the five-gate delivery table and mark what is open vs to-lock next.
7. Write `docs/gate-plan.md` (or equivalent) in Context and/or repo.

**Ask when needed**

- Stack not even sketched: language/framework + DB product?
- Must public demo exist in v1, or local-first OK?
- Hard free-tier constraint?
- Domain theme / must-have entities? geometry/PostGIS now or later?

**Exit criteria**

- Architecture sketch + domain outline saved; open questions listed for Lock.
- No scaffolded product code; no paid hosting invented.

**Do not:** scaffold code, finalize stack/host locks, or deploy.

---

```
╔══════════════════════════════════════════════════════════════════╗
║  GATE — LOCK                                                     ║
╚══════════════════════════════════════════════════════════════════╝
    Stack, host, DB, DNS, repos, free-tier, domain model — lock once.
```

**Purpose:** Turn Plan into durable locks and delivery/QA sketch so Scaffold/Build never reopen basics.

**Steps**

1. Map Plan components onto the stack (routes, services, driver, migrations, env).
2. Lock table: app, DB product/project, CI, host, hostname, validation status, out-of-scope.
3. Lock domain model at entity level (tables/roles; PostGIS yes/no; seed source yes/no).
4. Ballpark monthly cost Free vs escape hatch (DB + host + CI) from public pricing — date-stamp; defer Cursor $ to Ship.
5. Document free-tier hard limits and minimal path: branch → PR → CI → merge → host autodeploy → migrate → smoke.
6. Env/secrets table (`DATABASE_URL`, `PORT`, `LOG_LEVEL`, `PUBLIC_HOSTNAME`) — where each lives; never in git. Prefer **pooler** URI for hosted API.
7. Write one `docs/lock-and-plan.md` (or update prefs); **stop for user approval**.

**Ask when needed**

- Always-on public demo? (forces paid-escape awareness)
- Region preference for DB vs host (egress)?
- CI on release mirror only, or also private Codebase?
- Who mirrors Codebase → release mirror (human WSL vs agent)?
- One shared Free DB for demos OK?
- Domain seed: GeoJSON API endpoint, auth, pagination, id field (see GeoJSON API section)?

**Exit criteria**

- Locks written to prefs + lock doc; user approved.
- Cost band and CI/deploy sketch agreed; no scaffolding yet.

**Do not:** create host services, burn preview envs, scaffold full product, or invent Cursor usage dollars.

---

```
╔══════════════════════════════════════════════════════════════════╗
║  GATE — SCAFFOLD                                                 ║
╚══════════════════════════════════════════════════════════════════╝
    Repo framework + design artifacts; not full productize.
```

**Purpose:** Land framework, design docs (md/sql/json), migration design, and CI stubs so Build has a clean base.

**Steps**

1. Scaffold package layout, entrypoints, stub mode (empty `DATABASE_URL` → in-memory), CI workflow, `.env.example` (no values).
2. Write design overview + schema SQL/JSON + API design JSON from locked domain.
3. Choose validation status (`422` recommended for FastAPI) if not already locked.
4. If a GeoJSON API seed is in scope: document provider pattern (see GeoJSON API section) + env placeholders; treat as **optional**.
5. Document migrate apply order (`psql -f migrations/00N_…`).
6. Draft Origin/Codebase PR for the framework.

**Ask when needed**

- Sample theme details still open after Lock?
- Public hostname / DNS ownership if not locked?
- Seed provider details still open (endpoint, auth, id field)?

**Exit criteria**

- Framework + design artifacts + CI stub merged or draft-PR-ready; stub tests green.
- No live deploy required; stack locks not reopened.

**Do not:** full production hardening, live deploy, or reopen stack without confirmation.

---

```
╔══════════════════════════════════════════════════════════════════╗
║  GATE — BUILD                                                    ║
╚══════════════════════════════════════════════════════════════════╝
    Implement APIs, sync, PostGIS/columns as needed, tests — then stop for deployable merge.
```

**Purpose:** Productize from Scaffold: implement routes/services, sync, spatial/columns as locked, tests; keep tooling eval inside the same thread (no separate “final plan” agent unless asked).

**Steps**

1. Reaffirm locks; diff “already on `main`” vs remaining gaps; ordered work packages in-thread.
2. Implement API + persistence; parameterized SQL; `LIMIT` on lists.
3. Add sync / GeoJSON provider wiring if locked (mock in CI; trusted network if WAF).
4. Apply PostGIS / attribute columns / dual-write as the locked domain requires (versioned migrations).
5. Tooling keep/skip as needed (`uv`, Ruff, pytest, skip Alembic/paid APM unless unlocked); single worker on Free RAM.
6. Acceptance per package; draft Origin PRs (one PR per coherent package).
7. Definition of done for Build; get approval before first deployable merge if scope is large.

**Ask when needed**

- Explicit approval to start / continue Build packages?
- Any package to waive or reorder?
- Which Cursor plan capabilities are fair game for DX?
- WAF / run-from-trusted-network for live sync?

**Exit criteria**

- Packages green (Ruff/pytest); migrations ready to apply; no greenfield scope creep.
- Ready for Ship (image/deploy) with a clear remirror request if runtime files changed.

**Do not:** deploy/remirror from Build itself unless user collapses Build+Ship; auto-upgrade paid hosting.

---

```
╔══════════════════════════════════════════════════════════════════╗
║  GATE — SHIP                                                     ║
╚══════════════════════════════════════════════════════════════════╝
    Deploy, mirror, smoke, version; costs/monitoring when asked.
```

**Purpose:** Get a verified live API: image from release mirror, pooler `DATABASE_URL`, smoke, optional semver, free monitoring; cost note only when requested.

**Steps**

1. Ensure tested Codebase `main` is mirrored to `<RELEASE_MIRROR>` `main` (human WSL/manual unless agents may push).
2. Build image from mirror `Dockerfile` on host; fix build errors before calling deploy done.
3. Wake Free DB if paused; apply versioned migrations in order.
4. Set host vars: **`DATABASE_URL`** = managed Postgres **pooler** URI (+ SSL), `LOG_LEVEL`, `PUBLIC_HOSTNAME`; `PORT` from host.
5. Deploy single replica / single worker; healthcheck `/health`; smoke health + one read path; optional DNS CNAME.
6. Free-tier monitoring only (host logs/metrics/healthcheck; DB advisors). Semver + changelog for **verified** behavior/API changes; draft Codebase PR; human remirrors; re-check OpenAPI version.
7. Cost/billing note only if asked: measured facts vs public limits; never invent Cursor $; point at Usage/Billing dashboards.

**Ask immediately if missing**

- Host login / project access? `DATABASE_URL` (pooler)? Mirror permission / “please mirror this SHA”? DNS credentials if hostname required now?
- Merge version PR / remirror when CI green? Defer version bump?
- Cost: document-only vs optimization actions? Optional Cloud Agents usage API key?

**Exit criteria**

- Live `/health` OK; deploy SUCCESS for mirrored SHA; version matches when bumped.
- Cost/monitoring documented only as requested; no invented Cursor dollar totals.

**Do not:** provision a second Postgres on the host; commit secrets; push release mirror without permission; greenfield features under Ship; invent monitoring SaaS.

---

```
╔══════════════════════════════════════════════════════════════════╗
║  LEGACY MAP  (stages 1–9 → gates)                                ║
╚══════════════════════════════════════════════════════════════════╝
    Archive only — do not reload stage-one…nine for routine work.
```

| Legacy stage | Gate |
| --- | --- |
| 1 Architecture | **Plan** |
| 2 Function/cost · 3 DevOps/QA | **Lock** |
| 4 Repo framework + design | **Scaffold** |
| 5 Tooling · 6 Final plans · implement | **Build** |
| 7 Deploy · 8 Eval/version · 9 Costs | **Ship** |

Old stage files: keep as archive; banner them “Superseded by Gate X — do not reload for routine work” when touched.

---

```
╔══════════════════════════════════════════════════════════════════╗
║  HOW DB + HOST CONNECT                                           ║
╚══════════════════════════════════════════════════════════════════╝
    Pooler DATABASE_URL on the host; migrate as an explicit step.
```

- App on `<HOST_SERVICE>` reads **`DATABASE_URL`** (managed Postgres **pooler** URI + SSL).
- Shared host vars (values secret — do not paste): `DATABASE_URL`, `LOG_LEVEL`, `PUBLIC_HOSTNAME`.
- `PORT` is set by the host; single worker on Free RAM; healthcheck **`/health`** (generous timeout, e.g. ~120s).
- Do **not** provision host Postgres when DB is already on Supabase-like Free. Wake Free DB if paused before migrate/sync.
- DNS: CNAME for `<PUBLIC_HOSTNAME>` → host target.

**Where secrets live**

| Secret | Where to read (not invent) |
| --- | --- |
| `DATABASE_URL` | Host service variables · or DB dashboard → **Pooler** URI |
| Deploy source | `<RELEASE_MIRROR>` connected to host service |
| Local `.env` | Trusted local/WSL only; never commit |

---

```
╔══════════════════════════════════════════════════════════════════╗
║  REPO WIRING · MIRROR RUNBOOK  (trusted machine / WSL)           ║
╚══════════════════════════════════════════════════════════════════╝
    Codebase merge → human mirror → host rebuild → smoke.
```

Flow: merge on Origin/Codebase → human **batch**-mirrors from WSL/trusted machine → host rebuilds → smoke live host. **Skip** remirror/rebuild for Context-only or non-image docs.

```bash
# On trusted machine — Codebase is source of truth
cd <LOCAL_DEV_PATH>
git fetch origin && git checkout main && git pull origin main

# Mirror tested main → release remote (human only unless user grants agents)
# Typical shape (adjust remotes as configured):
git push git@github.com:<RELEASE_MIRROR>.git main:main
# or: git push github main:main
```

After mirror: confirm host deploy SUCCESS for that SHA, then:

```bash
curl -sS https://<PUBLIC_HOSTNAME>/health
curl -sS https://<PUBLIC_HOSTNAME>/openapi.json | python3 -c 'import sys,json; print(json.load(sys.stdin)["info"]["version"])'
```

Expect health roughly `{"status":"ok","database":"connected"}` and version matching the package/OpenAPI bump.

---

```
╔══════════════════════════════════════════════════════════════════╗
║  GEOJSON API PROVIDER  (optional seed pattern)                   ║
╚══════════════════════════════════════════════════════════════════╝
    Fetch features → transform → upsert; trusted network if WAF.
```

Optional pattern when seeding layers/objects from an external **GeoJSON API provider** (not project-identity). Wire env as placeholders only — never commit tokens.

| Placeholder | Role |
| --- | --- |
| `<GEOJSON_API_BASE_URL>` | Provider root (scheme + host [+ API prefix]) |
| `<LAYER_OR_COLLECTION_PATH>` | Layer / collection / query path under the base |
| `<SOURCE_OBJECT_ID_FIELD>` | Stable id in feature `properties` (or top-level) for upsert |

**Approaches** (pick one after asking; document in sync runbook):

1. **List → detail (wget-friendly)** — page or list ids, then fetch each feature (or small id batches) as GeoJSON; shell owns HTTP/`wget`, Python owns parse + Postgres upserts.
2. **Paged FeatureCollection** — request `FeatureCollection` pages (`offset`/`limit`, `resultOffset`/`resultRecordCount`, cursor, or link header); stream page → upsert; never load the whole layer into RAM.
3. **Hybrid** — list ids with a light endpoint, detail/GeoJSON for geometry + attributes.

| Role | Owns |
| --- | --- |
| Shell wrapper script | Pages, retries, temp files, timeouts, rate-limit backoff |
| HTTP tool (`wget` / `httpx`) | Fetch list / pages / detail GeoJSON |
| Python | Transform Feature(s) + parameterized upserts only |

**Rules of thumb**

- Discover page size / pagination support from provider metadata when available; never assume one query returns all rows.
- Upsert on `(layer_id, <SOURCE_OBJECT_ID_FIELD>)` or equivalent unique key.
- Soft time limit OK (committed batches keep); **mock the provider in CI** — never hit the live API from Actions.
- **WSL / trusted network only for live sync:** cloud agent IPs are typically WAF-blocked — **never assign live GeoJSON/external sync to cloud agents**. Run from `<LOCAL_DEV_PATH>` / WSL; do not burn agent minutes retrying.
- Auth headers/tokens live in env only (local/WSL or host secrets) — never paste into docs/chat.

**Ask user only if needed** (when seed is in scope)

- Endpoint: full `<GEOJSON_API_BASE_URL>` + `<LAYER_OR_COLLECTION_PATH>`?
- Auth: none, API key, bearer, or other (and where the secret already lives)?
- Pagination style: offset/limit, ArcGIS-style offsets, cursor, link header, or list→detail?
- Id field for upsert (`<SOURCE_OBJECT_ID_FIELD>`)?
- WAF / run-from-trusted-network: must sync run from user WSL (or similar)?
- Rate limits / polite delays / max concurrency?

*(Optional one-line example only: some public MapServer/FeatureServer portals expose paged GeoJSON — treat like any other provider.)*

---

```
╔══════════════════════════════════════════════════════════════════╗
║  LOCAL DEV RUNBOOK                                               ║
╚══════════════════════════════════════════════════════════════════╝
    uv sync → .env pooler → run → migrate → test.
```

```bash
cd <LOCAL_DEV_PATH>   # or this checkout
uv sync --group dev
cp .env.example .env          # set DATABASE_URL (pooler); never commit
uv run <app-entrypoint>       # local port per README
curl -sS http://127.0.0.1:<PORT>/health

psql "$DATABASE_URL" -f migrations/001_….sql
# … apply 002, 003, … in order

# Optional GeoJSON provider sync — trusted network if WAF
# timeout 300 ./scripts/<geojson-sync-script>

uv run ruff check src tests && uv run ruff format --check src tests && uv run pytest -q
```

Empty `DATABASE_URL` → in-memory stub (CI-friendly).

---

```
╔══════════════════════════════════════════════════════════════════╗
║  LIVE SMOKE · HEALTH                                             ║
╚══════════════════════════════════════════════════════════════════╝
    Manual curl against public hostname; free monitoring only.
```

Target: **`https://<PUBLIC_HOSTNAME>`**

| Call | Expect |
| --- | --- |
| `GET /health` | 200 `ok` / `connected` (or documented stub/skipped) |
| Primary list/read paths | 200; empty collection OK if no seed yet |
| `GET /openapi.json` → `info.version` | Matches package after mirror of version bump |

Free-tier monitoring only (host health/logs/metrics, DB advisors). No paid APM.

---

```
╔══════════════════════════════════════════════════════════════════╗
║  DOC MAP                                                         ║
╚══════════════════════════════════════════════════════════════════╝
    Prefer docs/ + Context store over chat history.
```

| Path | Purpose |
| --- | --- |
| `<CONTEXT_STORE>/docs/agent-bootstrap.md` | **Default** fresh-agent entry (~80–120 lines, 5 gates) |
| This file (`docs/general-agent-instructions.md`) | New-project template / full gate detail (not routine Build/Ship reload) |
| Project-specific brief (if any) | Filled placeholders / live topology |
| `<CONTEXT_STORE>/preferences.md` | Reusable user prefs |
| `<CONTEXT_STORE>/docs/flow-simplification-recommendations.md` | Cheaper ops; 5-gate recommendation |
| Gate / legacy stage plans | Prefer gate docs; stage-one…nine = archive |
| GeoJSON provider sync docs (if any) | Pagination / list→detail / upsert / trusted-network runbook |
| DNS notes (if any) | CNAME setup for `<PUBLIC_HOSTNAME>` |
| `<CONTEXT_STORE>/notes.md` | Short current status |
| Installed MCP / plugins | Live DB, host, forge, Cursor cloud ops (see Context · plugins section) |

---

```
╔══════════════════════════════════════════════════════════════════╗
║  DO / DON’T                                                      ║
╚══════════════════════════════════════════════════════════════════╝
    Credential blockers → ask now; never paste secrets.
```

**Do:** free-tier bias · parameterized SQL · versioned migrations · draft/batch Codebase/Origin PRs · watch CI · ask on credential / plugin-auth blockers · **Context SoT** (`docs/` + bootstrap + `notes.md` + `preferences.md`) · use installed MCP/plugins (discover schemas first) · **WSL-only live GeoJSON sync** · pooler `DATABASE_URL` on host · operate by **5 gates** · skip remirror/rebuild for Context-only / non-image docs.

**Don’t:** bury lasting decisions only in chat · reinvent list-tables / migrations / deploy status / PR ops when plugins provide them · push `<RELEASE_MIRROR>` without permission · commit/paste secrets · load GeoJSON providers without pagination · **assign live sync to cloud agents** · reopen locked stack without confirmation · invent dollar figures for Cursor usage · provision duplicate host Postgres · reload full general-agent-instructions or stages 1–6 on routine Build/Ship · remirror for docs-only days.

---

```
╔══════════════════════════════════════════════════════════════════╗
║  FRESH-AGENT HANDOFF TEMPLATE                                    ║
╚══════════════════════════════════════════════════════════════════╝
    Fill placeholders; point at notes.md; state gate tip.
```

- Live API version / hostname: `https://<PUBLIC_HOSTNAME>` — host `<HOST_PROJECT>` / `<HOST_SERVICE>`.
- DB: `<DB_PROJECT>`; pooler `DATABASE_URL` on host (do not paste).
- Deploy path: Origin/Codebase `<DEV_REPO>` → human WSL/manual mirror → `<RELEASE_MIRROR>` → host. Agents never push the mirror unless granted.
- Current **gate** tip + links: `<CONTEXT_STORE>/notes.md` and `<CONTEXT_STORE>/docs/` (bootstrap → agents-instructions → this template only when inventing a sibling project).
- Use installed MCP/plugins for live ops; ask immediately on credential or plugin-auth gaps.
