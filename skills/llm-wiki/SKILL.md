---
name: llm-wiki
description: "Maintains a persistent LLM-written markdown wiki between immutable raw sources and answers so knowledge compounds instead of being re-derived each query. Triggers: llm wiki, persistent wiki, personal knowledge base, wiki maintenance, ingest sources, compound knowledge, index.md, log.md, Obsidian wiki, cross-references, Karpathy wiki pattern. Uses: Read, Glob, Grep, file edits, optional WebSearch, AskUserQuestion when schema or goals are ambiguous. Outputs: updated wiki pages, index, append-only log, citations on query. Do NOT use for: mutating raw sources, one-off chat answers with no wiki artifact, or replacing a user-defined wiki schema without reading it first."
metadata:
  tags: knowledge-base, wiki, markdown, obsidian, ingest, rag-alternative, personal-kb, research-notes
---

# llm-wiki

Build and maintain a **structured, interlinked markdown wiki** that sits between curated **raw sources** and **questions**. Cross-references, synthesis, and flagged contradictions are updated when sources change—knowledge compounds rather than being rediscovered on every query.

Directory layout is **defined by the user's schema** (often a file such as `AGENTS.md`, `CLAUDE.md`, or a dedicated wiki config). This skill defines **behaviors**, not fixed paths—always read the schema first.

**Companion rules (detail):** [index-log conventions](./rules/index-log-conventions.md), [optional tooling](./rules/optional-tooling.md).

## Non-negotiables

1. **Read the wiki schema before editing.** If none exists, ask via `AskUserQuestion` whether to create a minimal one (paths for raw sources, wiki root, index and log filenames, conventions). Do not invent large directory trees without confirmation.
2. **Never modify raw sources.** Treat them as immutable evidence. All synthesis, summaries, and entity pages live in the wiki layer only.
3. **On every ingest:** Integrate new information into the wiki—entity or topic pages, summaries, cross-links—not only a single dump file. Update the **index** and append to the **log** as specified by the schema.
4. **Flag contradictions explicitly.** When a new source conflicts with an existing wiki claim, update the relevant pages and record the conflict (dated note, "see also", or a dedicated section per schema).
5. **On query:** Prefer reading the **index** first to locate pages, then drill into linked pages. Answer with **wiki-backed citations** (page paths or section anchors). If the schema allows it, offer to **file** a durable answer (new page or section) so the exploration compounds.
6. **On lint:** Proactively look for stale claims, orphan pages (no inbound links), missing pages for important concepts, broken links, and gaps fixable with a targeted web search—then report or fix per user instruction.
7. **Keep the log append-only** with consistent, parseable headings (e.g. `## [YYYY-MM-DD] ingest | <title>`) so the timeline is greppable.

## The three layers

| Layer | Role | Who edits |
|-------|------|-----------|
| **Raw sources** | Articles, papers, clips, assets—evidence | Human curates; agent **reads only** |
| **Wiki** | Summaries, entity/topic pages, synthesis, links | **Agent** creates and maintains |
| **Schema** | Where things live, naming, categories, workflows | **Human and agent** co-evolve |

## Process

### Ingest

1. Locate the new source under the raw tree per schema.
2. Read it (and attachments if the schema says how).
3. Extract entities, claims, and relationships; map them to existing wiki pages.
4. Create or update all affected wiki pages; add outbound and inbound links where the schema expects them.
5. Update the **index** (one-line blurbs, categories, links).
6. Append an entry to the **log** describing what was ingested and which pages changed.
7. If the user wants discussion first, summarize takeaways and wait for direction before writing—still complete steps 4–6 once aligned.

### Query

1. Open the **index** (or search the wiki per schema / optional tools such as local search).
2. Read relevant pages; follow links for missing context.
3. Synthesize an answer with **citations** to wiki pages (and raw paths when citing evidence).
4. If the result is reusable (comparison, analysis, decision), add it to the wiki and index/log when the schema allows.

### Lint

1. Scan for contradictory statements across pages (same entity, incompatible facts).
2. Find **stale** claims where newer sources or pages supersede older text.
3. List **orphan** pages with no inbound wiki links.
4. List important terms that appear often but lack a dedicated page.
5. Note **missing cross-references** and **data gaps** (optional `WebSearch` to suggest sources—do not fabricate citations).
6. Present findings as a short report or apply fixes if the user asked for automatic repair.

## Index and log (conceptual)

Actual paths and categories come from the schema. For heading patterns, grep-friendly log lines, and index shape, see [index-log-conventions](./rules/index-log-conventions.md).

## Integrated example

**Schema says:** Wiki at `notes/wiki/`, raw at `notes/raw/`, index `notes/wiki/index.md`, log `notes/wiki/log.md`.

**User:** "Ingest `notes/raw/2026-04-06-neural-plasticity.md`."

**Agent actions:**

1. Read the raw file; do not edit it.
2. Find or create pages `notes/wiki/topics/neural-plasticity.md`, update `notes/wiki/people/<researcher>.md` if mentioned, and add a "See also" from `notes/wiki/overview.md` if that page exists.
3. Update `index.md` with links and one-line descriptions for new or changed pages.
4. Append to `log.md`: `## [2026-04-06] ingest | neural-plasticity` plus bullets listing touched files.
5. Tell the user what changed and surface one contradiction if the new source disagrees with `notes/wiki/topics/sleep-and-memory.md`.

This example satisfies: raw immutability, multi-page integration, index + log updates, and contradiction handling.

## Optional tooling

See [optional-tooling](./rules/optional-tooling.md) for local search (e.g. qmd), Obsidian-adjacent options, and git—never assumed unless the user says so.

## Anti-patterns

- **RAG-only mindset:** Retrieving chunks without updating the wiki—knowledge does not compound.
- **Editing raw sources** to "fix" typos or merge notes—violates the evidence layer.
- **Skipping index or log** after ingest—breaks navigation and history.
- **Chat-only answers** with no durable wiki update when the user is building a knowledge base.
- **Silent merges:** Hiding contradictions instead of surfacing and dating them.
- **Schema blindness:** Creating paths or filenames that contradict the user's wiki config.
- **Wrong scope:** Do not use this skill to replace a project's `AGENTS.md` or product docs (use a repo-specific agent-init skill), or to fabricate sources—mark gaps and suggest real sources.
