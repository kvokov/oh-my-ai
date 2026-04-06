---
name: agent-init
description: "Creates, updates, or prunes an AGENTS.md for any repository by auditing the codebase, detecting non-discoverable gaps, and drafting minimal high-signal instructions. Triggers: AGENTS.md, agent setup, agent config, init agents, workspace setup, agent instructions, Claude setup. Uses: Read, Bash, Glob, Grep, AskUserQuestion. Outputs: AGENTS.md at repo root (or module-level for monorepos). Do NOT use for: writing README, generating docs, configuring CI, or editing code."
metadata:
  version: "1.0.2"
  tags: initialization, agents, context-engineering, agents-md, workspace-setup
---

# agent-init

Generate or refine an `AGENTS.md` that contains only what agents cannot discover on their own — non-obvious commands, hidden constraints, workflow gotchas, and landmines.

## Non-negotiables

1. Always run the full repo audit (Phase 1) before asking any questions or drafting content — questions and content must be informed by actual findings, never generic.
2. Apply the discoverability filter to every line: if an agent can learn it from README, code, config, linter rules, CI, tests, or directory structure, exclude it from `AGENTS.md`. But distinguish **existence** from **intent**: a file or directory existing is discoverable, but its *status* (deprecated, do-not-touch, under migration) and *constraints* (must not enable, requires special flags) are often NOT discoverable. Include those.
3. Never include tech stack summaries, architecture descriptions, directory overviews, package listings, or rules already enforced by tooling (linters, type checkers, tests, CI). NEVER include generic advice like "write docstrings for all public functions", "keep functions under 50 lines", "add tests for new features", or "follow the project's coding style." These are not non-discoverable constraints.
4. Use `AskUserQuestion` for every developer-facing choice; always include an uncertainty / "I'm not sure" option.
5. If updating an existing `AGENTS.md`, improve incrementally — never blind-replace. Show a diff summary before writing. **"Incremental" means show a diff, NOT preserve everything.** Stale content, redundant content, tech stack sections, directory overviews, and tooling-enforced rules MUST be actively pruned from existing files. The quality gate applies equally to existing and new content.
6. For repositories with 5+ top-level modules or packages, recommend hierarchical `AGENTS.md` files near relevant modules instead of one monolithic root file.
7. Run the quality gate (Phase 4) before presenting the final draft. Delete any line that fails: non-discoverable AND accurate AND materially reduces mistakes.
8. Every item considered but omitted MUST appear in audit-notes.md or changes-summary.md with a one-line reason for exclusion. Silent omission is a failure mode — the reasoning trail is required.

## Output format (mandatory)

Every `AGENTS.md` you produce MUST follow this exact structure. Use these exact headings. Omit a section only if it has zero content — never rename or paraphrase the headings.

```markdown
# Agent instructions — <project name>

<One-line scope statement: one sentence explaining what this file covers and why it exists. Required.>

## Non-discoverable commands

<Commands with non-obvious flags, multi-step procedures, or names that differ from convention.>

## Landmines

<Deprecated directories still imported, hidden dependencies, files that must not be touched, order-sensitive operations.>

## Conventions not enforced by tooling

<Commit message formats without commitlint, naming patterns without lint rules, review requirements not in CI.>

## Task-specific constraints

<Per-area guidance: "when working in X, always Y because Z.">

## Scope & routing

<For monorepos: which modules have their own rules, where to find them.>
```

The **one-line scope statement** immediately after the `# Agent instructions` heading is required. It must be 1-2 sentences explaining what this file covers that agents cannot discover from reading the repo. Example: "Non-discoverable constraints for agents working in this repo."

## Process

### Phase 1 — Repo Audit

Scan the repository automatically. Do not ask the user anything yet.

Read and catalogue findings from these sources (skip any that do not exist):

| Priority | Files |
|----------|-------|
| Agent configs | `AGENTS.md`, `CLAUDE.md`, `.cursor/rules/`, `.cursorrules`, `.github/copilot-instructions.md`, `GEMINI.md` |
| Project docs | `README.md`, `PROJECT.md`, `CONTRIBUTING.md`, `CHANGELOG.md` |
| Tooling | `package.json`, `pyproject.toml`, `Cargo.toml`, `go.mod`, `Makefile`, `Justfile`, `Taskfile.yml` |
| CI / workflows | `.github/workflows/*.yml`, `.gitlab-ci.yml`, `Jenkinsfile`, `.circleci/config.yml` |
| Linter / formatter | `.eslintrc*`, `.prettierrc*`, `biome.json`, `.rustfmt.toml`, `ruff.toml`, `.editorconfig` |
| Config | `tsconfig*.json`, `next.config.*`, `vite.config.*`, `.env.example`, `docker-compose*.yml` |

For each source, extract:

- **Stack signals**: languages, frameworks, package managers, runtimes
- **Command quirks**: non-obvious flags, script names that differ from conventions, multi-step build/test commands
- **Constraint signals**: deprecated directories still imported, env vars required but not documented, feature flags, experimental modules
- **Existing agent instructions**: what's already covered (and whether it's stale or redundant)

Produce an in-memory **repo profile** — do not output it to the user. It feeds Phase 2.

See [audit-checklist](./rules/audit-checklist.md) for the detailed scan procedure.

### Phase 2 — Gap Detection

Compare the repo profile against any existing `AGENTS.md`:

- **Stale instructions**: reference removed tools, directories, or commands
- **Redundant instructions**: duplicate what's in README, enforced by linter/CI, or inferable from config
- **Missing coverage**: CI quirks not documented, non-obvious command flags, deprecated-but-still-imported paths, environment setup steps that aren't scripted

Classify each gap as:

| Category | Example |
|----------|---------|
| `command` | `npm test -- --no-cache` required but `package.json` script is just `vitest` |
| `landmine` | `/legacy` directory still imported in production via barrel file |
| `convention` | Commit messages must follow Conventional Commits but no commitlint configured |
| `routing` | Module `packages/auth` has its own deploy pipeline not documented anywhere |
| `env` | `DATABASE_URL` required but `.env.example` is missing or incomplete |

If there are genuine ambiguities (conflicting signals, unclear intent), queue 2–3 targeted questions for Phase 3. If no ambiguities exist, skip Phase 3 entirely.

### Phase 3 — Clarification (conditional)

Ask 2–3 questions maximum via `AskUserQuestion`. Only ask about genuine ambiguities surfaced by the audit — never ask generic questions.

Good questions are specific and informed:

- "CI runs `npm test -- --no-cache` but the `package.json` script doesn't include that flag — is `--no-cache` always required?"
- "The `/legacy` directory is imported in `src/index.ts` but looks unmaintained — should agents avoid touching it?"
- "I found 3 env vars in code that aren't in `.env.example` — are they required for local dev?"

Bad questions (never ask these):

- "What's your tech stack?" (discoverable)
- "Do you use TypeScript?" (discoverable)
- "What testing framework do you use?" (discoverable)

If no ambiguities exist, skip this phase and tell the user: "Audit was clean — no ambiguities found. Moving to draft."

### Phase 4 — Draft, Quality Gate, and Approval

Generate the `AGENTS.md` content using only material that passes the discoverability filter. See [discoverability-filter](./rules/discoverability-filter.md) for the filter specification. Use the exact structure defined in the **Output format** section above.

**Quality gate**: For every line in the draft, verify:

1. Is it non-discoverable from repo files alone? (Remember: a directory's *existence* is discoverable, but its *status* — deprecated, do-not-touch, under migration — is often NOT.)
2. Is it still accurate today (not referencing removed code/tools)?
3. Does it materially reduce mistakes, cost, or time?

Delete any line that fails any check. Log every removal in audit-notes.md or changes-summary.md with the reason.

Present the final draft to the user via `AskUserQuestion` with options:

- "Looks good — write it"
- "Needs changes" (free text)
- "Start over"

If updating an existing `AGENTS.md`, present a summary of changes: lines added, lines removed, lines modified.

Write the file only after explicit approval.

### Maintenance guidance

After writing `AGENTS.md`, remind the user:

> `AGENTS.md` is temporary guidance, not permanent configuration. When a recurring issue appears, prefer fixing the root cause in code or tooling (lint rule, test, script, config). Keep only the minimum instruction needed until the root cause is solved. Prune stale instructions aggressively.

## Example

> **Important**: The tech stack details below (NestJS, npm, Jest, etc.) appear ONLY to explain the audit reasoning. They must NEVER appear in actual `AGENTS.md` output. The only output is the final `AGENTS.md` content shown in Phase 4.

**Input**: A NestJS API with npm workspaces, an existing but stale `AGENTS.md`.

**Phase 1** finds CI uses `--no-cache` flag not in `package.json` scripts, an unmaintained `src/legacy-auth/` module still imported, and `REDIS_URL` missing from `.env.example`.

**Phase 2** classifies: `command` (hidden flag), `landmine` (legacy module), `env` (missing var), `stale` (existing "use yarn" instruction), `redundant` (existing directory overview).

**Phase 3** asks one question about the legacy module; user confirms it's under migration and should not be touched.

**Phase 4 produces** (note: uses the exact prescribed headings, includes scope statement, and the `## Landmines` section documents the deprecated directory's *status* — which is not discoverable even though the directory itself is visible):

```markdown
# Agent instructions — acme-api

Non-discoverable constraints for agents working in this repo.

## Non-discoverable commands

- Always run tests with `npm test -- --no-cache`. The `--no-cache` flag is required because Jest module caching breaks fixture isolation. Running without it produces false greens.
- Run database migrations with `npx typeorm migration:run -d src/data-source.ts`. There is no npm script — invoke directly via npx.

## Landmines

- `src/legacy-auth/` is imported in `app.module.ts` but is under active migration. Do not modify files in this module. If a task requires auth changes, work in `src/auth-v2/` instead.

## Conventions not enforced by tooling

- `REDIS_URL` is required for local development but is not in `.env.example`. Set it to `redis://localhost:6379` for local work.
```

**What is NOT included** (and must never be): tech stack, directory structure, package manager choice, testing framework, framework version — all discoverable from repo files.

## Anti-patterns

Avoid these failure modes — most violate the non-negotiables above:

- **Codebase tour**: describing architecture, directory tree, or tech stack. Agents can read the repo.
- **Generic advice**: "write clean code", "follow best practices". Not actionable, not specific.
- **Duplicating tooling**: restating rules already enforced by eslint, prettier, tsconfig, or CI.
- **Kitchen-sink file**: cramming everything into one long file. For large repos, use hierarchical files.
- **Blind replacement**: overwriting an existing `AGENTS.md` without showing what changed.
- **Asking discoverable questions**: "What language is this project in?" wastes the developer's time.
