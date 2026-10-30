# CursingStandards docs index

Entry point for agents. Load **this file first**, then only the linked leaf for the current task.

| id | summary | path |
|----|---------|------|
| desktop_recovery | Clean Cursor desktop restore checklist | [../desktop-cursor-recovery.md](../desktop-cursor-recovery.md) |
| desktop_recovery_reference | Redacted inventory (plugins, MCP bases, public repos) | [../desktop-cursor-recovery.reference.md](../desktop-cursor-recovery.reference.md) |
| agent_context_budget | Keep agent context small; retrieve on demand | [agent-context-budget.md](agent-context-budget.md) |
| agent_structured_docs | Hierarchical docs + handoff format for AI parsing | [agent-structured-docs.md](agent-structured-docs.md) |

## task_routing

| Task tag | Open first |
|----------|------------|
| `restore-cursor` | `desktop_recovery` |
| `install-plugins-mcp` | `desktop_recovery` then `desktop_recovery_reference` |
| `shrink-context` / `handoff` | `agent_context_budget` then `agent_structured_docs` |
| `write-docs` | `agent_structured_docs` |

## redaction

Public repo: no secrets, PATs, live project refs, private repo lists, or absolute username paths. See the “What never belongs” section in the recovery reference.
