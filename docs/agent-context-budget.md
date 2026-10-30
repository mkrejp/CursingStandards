# Agent context budget

Keep any agent working in this ecosystem’s context **as small as reasonably possible**. Prefer pointers and hierarchical docs over dumping whole repos or long histories into the prompt.

## Goals

- Minimize tokens loaded per turn without losing the facts needed for the current task.
- Prefer **retrieve on demand** over **preload everything**.
- Leave a trail of **structured, hierarchical documentation** so later agents can parse top-down and open only the next needed node.

## Rules for agents

1. **One job per session slice** — Do not load unrelated skills, MCP dumps, or full file trees “just in case.”
2. **Start from the index** — Open [docs/INDEX.md](INDEX.md) (or this repo’s README), then only the linked leaf for the task.
3. **Cite, don’t paste** — When referring to large specs, link the path and section heading; quote only the lines that change the decision.
4. **Cap exploration** — Prefer targeted search (`rg`, path globs) over recursive reads. Stop when the decision is grounded.
5. **Summarize before handoff** — End a turn or subagent with a short structured summary (see [agent-structured-docs.md](agent-structured-docs.md)), not a transcript dump.
6. **Secrets stay out** — Never enlarge context with tokens, PATs, or live env files.

## What to load by default

| Situation | Load |
|-----------|------|
| Clean Cursor restore | [desktop-cursor-recovery.md](../desktop-cursor-recovery.md) only; open reference if installing plugins/MCP |
| New feature in a project | That project’s `README` + `docs/INDEX.md` (if present) + the single owning module |
| Debug failure | Failing log/test excerpt + the one suspect file |
| Cross-repo standards | This file + [agent-structured-docs.md](agent-structured-docs.md) |

## What not to preload

- Entire plugin skill trees
- Full MCP tool schemas for unused servers
- Private inventory that is not required for the task
- Prior agent transcripts longer than a structured handoff block
- Generated lockfiles, build artifacts, or `node_modules`

## Subagents and parallel work

- Give each subagent a **narrow charter** and the **minimum file list**.
- Ask subagents to return **structured handoffs** (YAML or Markdown sections below), not narrative essays.
- Merge handoffs into the project’s hierarchical docs instead of keeping them only in chat.
