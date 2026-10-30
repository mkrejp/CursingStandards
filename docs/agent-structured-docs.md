# Structured documentation for AI hierarchical parsing

Agents must produce and maintain documentation that is **machine-navigable top-down**: a small index first, then stable section IDs, then leaf detail. Humans can read the same files; machines parse the hierarchy.

## Hierarchy contract

```text
docs/INDEX.md          ← entry: table of topics → relative links
docs/<domain>.md       ← one domain per file; H2 sections = stable anchors
docs/<domain>/<leaf>.md ← optional deeper leaves only when a domain file grows large
```

Rules:

1. **Single entry** — Every project that uses these standards has `docs/INDEX.md` (or links to this repo’s INDEX for shared standards).
2. **Stable headings** — Prefer `## snake_or_kebab_id — Title` or explicit HTML anchors so parsers can address sections without fuzzy search.
3. **One concern per file** — Split when a file exceeds ~200–300 lines or mixes unrelated domains.
4. **Front matter optional** — YAML front matter may include `id`, `summary`, `parent`, `children` for parsers:

```yaml
---
id: agent-context-budget
summary: Keep agent context small; retrieve docs on demand.
parent: index
children: []
---
```

5. **Tables for inventories** — Plugins, env vars, smoke tests, and checklists live in Markdown tables, not prose paragraphs.
6. **No secrets** — Same redaction rules as the desktop recovery guide.

## Required handoff block

When finishing a task (or returning from a subagent), emit this block so the next agent can parse without the transcript:

```markdown
## handoff
- goal: <one line>
- status: done | blocked | partial
- changed_paths:
  - path/a
  - path/b
- decisions:
  - <bullet>
- next:
  - <bullet>
- docs_updated:
  - docs/INDEX.md
  - docs/<file>.md#section
- do_not_load:
  - <paths or topics that are irrelevant next>
```

## Section template for domain docs

```markdown
# Domain title

One-sentence purpose.

## overview
...

## inventory
| Key | Value | Notes |
|-----|-------|-------|

## procedures
### procedure_id — Short name
1. Step
2. Step

## references
- [Related](./other.md#section)
```

## Parsing hints for agents

1. Read `docs/INDEX.md` first; follow only the link matching the task tag.
2. Within a file, jump to the `##` / `###` whose id matches the procedure; ignore sibling sections.
3. Prefer updating an existing section over appending unstructured notes at the bottom.
4. After edits, ensure INDEX still lists the file with a one-line summary.

## Anti-patterns

- Monolithic README that mixes restore, architecture, and runbooks
- Chat-log pastes into docs
- Duplicate copies of the same inventory in three files without a canonical link
- Unstable headings that change every edit (`## Updates from today`)
