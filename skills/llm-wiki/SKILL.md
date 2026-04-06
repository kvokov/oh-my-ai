---
name: llm-wiki
description: "Compiles and maintains a persistent LLM-written markdown wiki between immutable raw sources and answers—the Karpathy LLM Knowledge Base pattern. The agent writes and maintains the wiki; the human curates sources and reads it. Knowledge compounds instead of being re-derived each query. Triggers: llm wiki, persistent wiki, personal knowledge base, wiki maintenance, ingest sources, compound knowledge, index.md, log.md, Obsidian wiki, cross-references, Karpathy wiki pattern, compile wiki, knowledge base. Uses: Read, Glob, Grep, file edits, optional WebSearch, AskUserQuestion when schema or goals are ambiguous. Outputs: updated wiki pages, index, append-only log, citations on query, filed answers, visual outputs. Do NOT use for: mutating raw sources, one-off chat answers with no wiki artifact, or replacing a user-defined wiki schema without reading it first."
metadata:
  version: "1.2.0"
  tags: knowledge-base, wiki, markdown, obsidian, ingest, rag-alternative, personal-kb, research-notes
---

# llm-wiki

Build and maintain a **structured, interlinked markdown wiki** that sits between curated **raw sources** and **questions**. The agent **"compiles"** raw source material into wiki pages—summaries, concept articles, entity pages, cross-links—so knowledge **compounds** rather than being rediscovered on every query. The human curates sources and reads the wiki; the **agent writes and maintains it**.

Directory layout is **defined by the user's schema** (often a file such as `AGENTS.md`, `CLAUDE.md`, or a dedicated wiki config). This skill defines **behaviors**, not fixed paths—always read the schema first.

**Companion rules (detail):** [index-log conventions](./rules/index-log-conventions.md), [optional tooling](./rules/optional-tooling.md).

## Non-negotiables

1. **Read the wiki schema before editing.** If none exists, ask via `AskUserQuestion` whether to create a minimal one (paths for raw sources, wiki root, index and log filenames, conventions). Do not invent large directory trees without confirmation.
2. **Never modify raw sources.** Treat them as immutable evidence. All synthesis, summaries, and entity pages live in the wiki layer only.
3. **On every ingest:** Integrate new information into the wiki—entity or topic pages, summaries, cross-links—not only a single dump file. Update the **index** and append to the **log** as specified by the schema.
4. **Flag contradictions explicitly.** When a new source conflicts with an existing wiki claim, update the relevant pages and record the conflict (dated note, "see also", or a dedicated section per schema).
5. **On query:** Always **read `index.md` first** before reading any topic pages—use it to discover which pages to read. Then drill into linked pages. Answer with **wiki-backed citations** (page paths or section anchors). Always **file** the answer as a durable wiki page (new page or section) so the exploration compounds, in addition to any standalone output the user requested.
6. **On lint:** Proactively look for stale claims, orphan pages (no inbound links), missing pages for important concepts, broken links, and gaps fixable with a targeted web search—then report or fix per user instruction.
7. **Keep the log append-only.** Every log heading uses exactly one of three keywords—`ingest`, `query`, or `lint`—never synonyms like "add", "update", "answer", "comparison", "audit", "check", or "review". Format:
   - After an ingest: `## [YYYY-MM-DD] ingest | <title>`
   - After a query: `## [YYYY-MM-DD] query | <title>`
   - After a lint: `## [YYYY-MM-DD] lint | <title>`

## The three layers

| Layer | Role | Who edits |
|-------|------|-----------|
| **Raw sources** | Articles, papers, repos, datasets, images—evidence | Human curates; agent **reads only** |
| **Wiki** | Summaries, entity/topic pages, concept articles, synthesis, backlinks, visualizations | **Agent** writes and maintains; human **reads and reviews** |
| **Schema** | Where things live, naming, categories, workflows | **Human and agent** co-evolve |

## Process

### Ingest

1. Locate the new source under the raw tree per schema.
2. Read it (and attachments if the schema says how). If the source contains or references images, ensure they are stored locally (per schema) so the wiki can reference them with relative paths.
3. Extract entities, claims, and relationships; map them to existing wiki pages.
4. Create or update all affected wiki pages; add outbound and inbound links where the schema expects them.
5. Update the **index** (one-line blurbs, categories, links).
6. Append to `wiki/log.md` using keyword `ingest`: `## [YYYY-MM-DD] ingest | <short title>` followed by bullets listing touched files.
7. If the user wants discussion first, summarize takeaways and wait for direction before writing—still complete steps 4–6 once aligned.

### Query

1. **Read `wiki/index.md` first**—always start here to discover which topic pages exist and where they are. Do not jump directly to topic files.
2. Read the relevant topic pages found via the index; follow cross-links for missing context.
3. Synthesize an answer with **citations** to wiki page paths (e.g. `wiki/topics/foo.md`) and raw source paths when citing evidence.
4. **File the answer as a new wiki page** under the wiki tree (e.g. `wiki/topics/` or `wiki/comparisons/`). Update `wiki/index.md` with a link and one-line description for the new page. If the user also requested a standalone output file, produce that too—but always create the wiki page regardless.
5. Append to `wiki/log.md` using keyword `query`: `## [YYYY-MM-DD] query | <short title>`

### Lint

1. Scan for contradictory statements across pages (same entity, incompatible facts).
2. Find **stale** claims where newer sources or pages supersede older text.
3. List **orphan** pages with no inbound wiki links.
4. List important terms that appear often but lack a dedicated page.
5. Note **missing cross-references** and **data gaps** (optional `WebSearch` to impute missing data or suggest sources—do not fabricate citations).
6. Identify **interesting connections** between topics that could warrant new article candidates, and suggest **further questions** worth investigating.
7. Present findings as a short report or apply fixes if the user asked for automatic repair.
8. Append to `wiki/log.md` using keyword `lint`: `## [YYYY-MM-DD] lint | <short title>`

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

**User:** "Compare neural plasticity vs. synaptic pruning for my review. Save the comparison as `comparison.md`."

**Agent actions:**

1. **Read `notes/wiki/index.md` first** to find relevant topic pages.
2. Read the topic pages discovered via the index; follow cross-links.
3. Synthesize the comparison with citations to wiki page paths and raw source paths.
4. File as `notes/wiki/topics/plasticity-vs-pruning.md` (durable wiki page). Also create `comparison.md` at the workspace root as the user requested.
5. Update `notes/wiki/index.md` with a link and one-line description for the new wiki page.
6. Append to `notes/wiki/log.md`: `## [2026-04-06] query | plasticity vs pruning` plus bullets listing the new page and pages consulted.

This example satisfies: index-first file access, wiki-backed citations, durable answer filed into wiki, standalone output also produced, index updated, and correct `query` log keyword.

## Optional tooling

See [optional-tooling](./rules/optional-tooling.md) for local search (e.g. qmd), Obsidian-adjacent options, and git—never assumed unless the user says so.

## Scaling

At small scale (~100 articles, ~400K words), the index + grep approach works well: the agent reads the index to navigate, follows links, and synthesizes answers without needing vector search or RAG. As the wiki grows, consider layering in additional tools (see [optional tooling](./rules/optional-tooling.md)): local search engines over the wiki, CLI tools the agent can invoke for larger queries, or even synthetic data generation and finetuning to embed wiki knowledge into model weights. But always start simple—the index-first pattern is surprisingly effective.

## Anti-patterns

- **RAG-only mindset:** Retrieving chunks without updating the wiki—knowledge does not compound.
- **Editing raw sources** to "fix" typos or merge notes—violates the evidence layer.
- **Skipping index or log** after ingest—breaks navigation and history.
- **Chat-only answers** with no durable wiki update when the user is building a knowledge base.
- **Silent merges:** Hiding contradictions instead of surfacing and dating them.
- **Schema blindness:** Creating paths or filenames that contradict the user's wiki config.
- **Wrong scope:** Do not use this skill to replace a project's `AGENTS.md` or product docs (use a repo-specific agent-init skill), or to fabricate sources—mark gaps and suggest real sources.
