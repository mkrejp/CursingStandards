# CursingStandards

Shared standards for Cursor, Railway, and GitHub project flow.

**Agents:** start at [docs/INDEX.md](docs/INDEX.md). Keep context small; follow hierarchical docs.

## Desktop Cursor recovery

Public, **redacted** restore guide for a clean Cursor desktop install:

- [desktop-cursor-recovery.md](desktop-cursor-recovery.md) — ordered checklist
- [desktop-cursor-recovery.reference.md](desktop-cursor-recovery.reference.md) — inventory (plugins, MCP bases, public repos)

## Agent context and documentation

- [docs/agent-context-budget.md](docs/agent-context-budget.md) — keep agent context as small as reasonably possible
- [docs/agent-structured-docs.md](docs/agent-structured-docs.md) — structured docs and handoff blocks for AI hierarchical parsing
- [docs/postgresql.md](docs/postgresql.md) — PostgreSQL schema, query, index, and migration rules
- [docs/python.md](docs/python.md) — Python structure, typing, pytest, uv, and Ruff rules

Do not store secrets, PATs, live project refs, or private repo lists in this repository. Re-authenticate each MCP on a new machine.
