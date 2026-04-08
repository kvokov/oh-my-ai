---
name: skill-maker
description: "Use this skill any time someone wants to create, scaffold, build, fix, improve, benchmark, or optimize a Tessl/Claude skill — even if they don't say 'tessl' explicitly. If the request involves making a new skill ('create a skill for X', 'build me a skill that does Y', 'scaffold a skill called Z'), fixing or completing an existing one (missing tile.json, broken repo integration, low eval scores, description not triggering), or running and iterating on evals, invoke this skill. The full workflow covers: structured interview → SKILL.md + tile.json + rules/ scaffolding → README/CI repo integration → tessl tile lint → optional Tessl CLI pipeline (skill review, scenario generate/download, eval run) → hand-authored evals or LLM-as-judge fallback → benchmark logging. Do NOT use for: editing application code, debugging, refactoring, writing general documentation, or creating presentations."
metadata:
  version: "1.0.2"
  tags: skill, create, scaffold, interview, eval, optimize, benchmark, tile, tessl
---

# skill-maker

Create production-quality Tessl skills from scratch and optimize them through eval-driven iteration.

Two modes of operation:

- **Create:** Interactive interview → scaffold a complete skill directory → repo integration → lint → (when Tessl CLI exists) §2.6 pipeline through scenarios and eval.
- **Optimize:** Refresh scenarios (§2.6 Path T or Phase 3 Path M) → run evals → analyze results → propose edits → apply → re-eval → log.

Detect which mode from the user's request. If ambiguous, ask.

## Non-negotiables (do not skip)

1. **Always use AskUserQuestion** for every user-facing choice. Never assume answers.
2. **Every AskUserQuestion must include an uncertainty option** ("I'm not sure — help me decide"). When selected, either infer from prior answers or trigger a research loop.
3. **Run all interview questions before scaffolding.** Do not generate SKILL.md mid-interview.
4. **Scaffold must lint-pass.** After generating files, run `tessl tile lint` if available; otherwise run simulated lint checks (Phase 2.5).
5. **Repo integration is mandatory.** Every new skill gets a README row and a CI matrix entry. Check for existing entries before inserting (idempotency). **Do not add or edit `skills-lock.json`** — it pins vendored skills under `.agents/skills/`; first-party tiles live under `skills/` and are wired via README + CI only.
6. **Eval execution is read-only.** Never modify skill files during `tessl eval run` or LLM-as-judge runs. All skill mutations (e.g. `tessl skill review`, Phase 5 apply) must occur **before** eval execution starts, at explicit workflow boundaries — never interleaved with a running eval.
7. **Benchmark logging is append-only.** Never overwrite or delete previous entries in benchmark-log.md.
8. **Present optimization proposals as specific edits** with before/after text, not vague suggestions. **`tessl skill review --optimize --yes` is an exception:** Tessl applies changes immediately without per-edit approval — treat it as a distinct "Tessl apply" step and tell the user when you run it.
9. **Every SKILL.md includes `metadata.version`.** Use a semver string (e.g. `1.0.0` for new skills; bump minor or patch when behavior or documentation meaningfully changes).

---

## Mode: Create

### Phase 1 — Interview

Run all 10 questions using AskUserQuestion before generating any files. Collect answers into a working decision map held in memory. Complete the **Completeness check** at the end before proceeding.

| # | Question | Key options | If unsure |
|---|----------|-------------|-----------|
| 1 | What does this skill do? (one sentence) | Free text | Ask "What task should the AI do better?" and "What goes wrong without it?" |
| 2 | Who will use this skill? | Developers / Semi-technical / Both | Default to Both |
| 3 | What type of project? | Code generation / Writing / Tool use / Interview / Other | Ask for a brief domain description |
| 4 | What are the 3–5 things this skill MUST do every time? | Free text (list) | Ask "What would make you say 'it worked perfectly'?" |
| 5 | What should this skill NEVER do? | Free text (list) | Generate domain-specific anti-patterns from purpose + domain answers |
| 6 | What phrases or signals activate this skill? | Free text / Generate suggestions / Research similar | Produce ≥5 candidate trigger terms from purpose + domain + behaviors; present for approval |
| 7 | What does the final output look like? | Files / Structured message / Interactive flow | Research similar skills |
| 8 | Does this skill need companion files beyond SKILL.md? | No / Rules files / Templates | Recommend companion files if >5 core behaviors or estimated length >300 lines |
| 9 | Which tools does this skill need? | AskUserQuestion only / + file tools / + WebSearch / All + Bash | Infer from domain: Code-gen → file tools + Bash; Writing → file tools; Workflow → all; Interview → AskUserQuestion + optionally WebSearch |
| 10 | Describe 2–3 realistic test tasks for this skill | Free text / Generate / Skip | Generate from purpose + behaviors |

**Completeness check:** Before scaffolding, verify all 10 categories have resolved values. If any are missing or still "unsure," resolve them before continuing.

---

### Phase 2 — Scaffold Generator

Turn the decision map into a complete skill directory. See [scaffold-rules](./rules/scaffold-rules.md) for full implementation details.

#### 2.1 SKILL.md

**Frontmatter:**
```yaml
---
name: <skill-name>
description: "<Purpose>. Triggers: <trigger terms>. Uses <tools>. Outputs: <deliverables>. Do NOT use for: <exclusions>."
metadata:
  version: "1.0.0"
  tags: <domain tags>
---
```

Apply [activation-design](./rules/activation-design.md) heuristics: front-load trigger terms; use imperatives throughout ("Use X", "Do not Y", "Always Z") — never "consider", "may want", or "try to".

**Body structure:** title + one-liner → non-negotiables (numbered) → process/phases → integrated example (realistic, exercises ≥2 non-negotiables) → anti-patterns.

**Length target:** 150–400 lines. If content exceeds 400 lines, extract secondary rules into `rules/*.md` and reference with relative links.

#### 2.2 tile.json

```json
{
  "name": "oh-my-ai/<skill-name>",
  "version": "1.0.0",
  "private": false,
  "summary": "<one-line purpose>",
  "skills": { "<skill-name>": { "path": "SKILL.md" } }
}
```

#### 2.3 Companion files (conditional)

If the interview identified companion file needs — **Rules:** `rules/<rule-name>.md` with YAML frontmatter (`name`, `description`) and structured content. **Templates:** at skill root, referenced from SKILL.md with relative links.

#### 2.4 Repo integration (automated, idempotent)

Both integrations are mandatory. Check for existing entries before inserting.

- **README.md:** Insert a new alphabetically-ordered row in the `| Skill | Description |` table.
- **CI matrix** (`.github/workflows/tessl-publish.yml`): Append `- skills/<skill-name>` to the `tile:` array. Validate YAML after editing (`python3 -c "import yaml; yaml.safe_load(open(...))"`) — revert and report on failure.

#### 2.5 Lint check

**With tessl CLI:** `cd skills/<skill-name> && tessl tile lint`

**Simulated (no CLI):** Verify: SKILL.md has valid YAML frontmatter with `name`, `description`, and `metadata.version` (semver string); tile.json has `name`, `version`, `summary`, `skills`; tile.json `name` matches `oh-my-ai/<skill-name>`; no broken relative links; each `rules/*.md` has YAML frontmatter with `name` and `description`.

Report results. Fix failures and re-lint.

#### 2.6 Tessl CLI pipeline (preferred when CLI exists)

Run from **repository root** with paths like `./skills/<skill-name>`. Use `which tessl` (or equivalent) first; if missing, skip this subsection and use Phase 3 Path M + Phase 4 Path B as needed.

**Boundary:** `tessl skill review` **writes** the skill and must complete before `tessl eval run` starts (→ non-negotiable #6).

| Step | Command / action |
|------|------------------|
| 1 | `tessl skill review --optimize --yes ./skills/<skill-name>` — may rewrite `SKILL.md` (and other files per Tessl). This is **Tessl auto-apply** (non-negotiable #8). |
| 2 | If the skill has `tile.json`: `cd skills/<skill-name> && tessl tile lint` — same as Phase 2.5. |
| 3 | `tessl scenario generate ./skills/<skill-name>` — **parse the generation id from stdout**; do not guess. |
| 4 | `tessl scenario download <generation>` — use the id from step 3. |
| 5 | **Place** downloaded scenarios under the skill: if Tessl wrote `./evals/` at repo root, move it with `mv ./evals/ ./skills/<skill-name>/` (or merge — see below). If output landed elsewhere, move **that** directory into `skills/<skill-name>/evals/`. |
| 6 | Continue to Phase 4 — Path A in [eval-runner](./rules/eval-runner.md). |

**If `skills/<skill-name>/evals/` already exists:** Use AskUserQuestion before moving: replace entirely, merge (explain how), or download to a temp directory — never overwrite silently.

Local Tessl cache under `.tessl/` stays out of git (typically gitignored).

---

## Mode: Optimize

### Phase 3 — Eval Scenario Generator

If the skill has no `evals/` directory, or the user asks for eval scenarios, offer to create them via AskUserQuestion.

**Path T — Tessl CLI (preferred):** Run steps 3–5 from §2.6: `tessl scenario generate` → parse generation id → `tessl scenario download` → place under `skills/<skill-name>/evals/`. To tune the skill before generating scenarios, run steps 1–2 from §2.6 first. After download, verify coverage against [benchmark-loop](./rules/benchmark-loop.md); add or adjust scenarios by hand if gaps remain.

**Path M — Manual (fallback):** Use when Tessl is missing, the user declines CLI generation, or download fails. Author scenarios directly.

Generate **2–3 scenarios** (or validate CLI output) following [benchmark-loop](./rules/benchmark-loop.md) coverage rules (full scenario schema, scoring rules, and selection heuristics are defined there). For each scenario, ensure `evals/<scenario-slug>/` contains:

**task.md** — A realistic problem (100–300 words) reflecting actual user prompts. Not a toy example.

**criteria.json:**
```json
{
  "context": "Tests whether <specific capability>",
  "type": "weighted_checklist",
  "checklist": [
    { "name": "<criterion>", "max_score": N, "description": "<what to check>" }
  ]
}
```

Key constraints: all `max_score` values must sum to exactly 100; each criterion must be independently verifiable. Name scenarios as kebab-case slugs (e.g., `core-interview-flow`, `noisy-context-retrieval`).

---

### Phase 4 — Eval Runner

See [eval-runner](./rules/eval-runner.md) for full implementation (including the **full Tessl CLI pipeline** and `--json` vs `--agent`). Summary:

**Path A — Tessl CLI (preferred):** From repo root, `tessl eval run ./skills/<skill-name> --json` (add `--agent=...` when you need a fixed judge model; see eval-runner). Parse JSON output into per-scenario, per-criterion scores.

**Path B — LLM-as-Judge Fallback:** For each scenario, run two subagents (Agent tool) — one with task only (baseline), one with SKILL.md prepended (with-skill). Score each criterion by launching a judge subagent with the criterion description and agent output; request a JSON response `{"score": N, "reasoning": "..."}`.

Assemble results into a unified schema: date, method, model, scenarios (each with baseline score, with-skill score, delta, and per-criterion breakdown).

**Calibration:** If both paths are available, run both on the same scenarios. Accept if within ±15%; otherwise flag to user and prefer CLI results.

---

### Phase 5 — Optimizer

Analyze eval results, classify failures, and propose targeted edits. See [activation-design](./rules/activation-design.md) and [benchmark-loop](./rules/benchmark-loop.md) for full failure pattern definitions and classification guidance.

#### 5.1 Failure classification

| Pattern | Signal | Fix |
|---------|--------|-----|
| Activation gap | Skill didn't fire / agent ignored instructions | Add explicit triggers to description; front-load non-negotiables |
| Ambiguous instruction | Inconsistent behavior across runs | Replace "consider"/"may want" with imperatives |
| Missing example | Agent doesn't know expected output shape | Add integrated example showing input → decision points → output |
| Regression | Negative delta vs. baseline | Identify which edit caused it; revert or rewrite |
| Context overload | Skill too long, agent loses focus | Compress; extract rules to companion files |

#### 5.2 Analysis workflow

1. Rank scenarios by delta (lowest first).
2. Within each low-scoring scenario, identify criteria scoring below 50% of their max_score.
3. Classify each into a failure pattern from the table above.
4. For the top 3 failures, propose specific edits: exact before/after text, failure pattern addressed, estimated score improvement.
5. Present proposals to the user. Do not apply without approval.

#### 5.3 Apply and re-eval

On user approval: apply edits → re-run Phase 4 → compare new vs. previous results → log to benchmark-log.md → flag any negative deltas immediately.

**Optional Tessl loop:** Before Phase 4, re-run **§2.6** from step 1 (`tessl skill review` through scenario refresh) to regenerate scenarios after major skill changes. All such mutations must finish before eval execution begins (→ non-negotiable #6).

---

### Phase 6 — Benchmark Logging and Gate Check

#### 6.1 Benchmark log

After every eval run, append to `skills/<skill-name>/benchmark-log.md`:

```markdown
## Run: <ISO-8601 timestamp>

**Method:** <tessl-cli | llm-as-judge> | **Model:** <model-name>

| Scenario | Baseline | With Skill | Delta |
|----------|----------|------------|-------|
| <name>   | <score>  | <score>    | <+/-N> |

**Changes applied:** <summary of edits, or "Initial evaluation">

---
```

Create the file if it doesn't exist. Always append — never overwrite.

#### 6.2 Gate check

- **Negative delta on any scenario:** WARN — "Scenario '<name>' regressed by <N> points. Review the last set of changes."
- **Any criterion at 0% with skill enabled:** WARN — "Criterion '<name>' in '<scenario>' scored 0 with the skill active. The skill may be actively interfering."
- **All deltas positive and ≥10 points:** PASS — "All scenarios show meaningful improvement."

Warnings do not block. If warnings exist, offer to run another optimization cycle (return to Phase 5).

---

## Integrated example

**User says:** "Create a skill for writing git commit messages"

**Interview summary → decision map:**

| # | Answer |
|---|--------|
| 1 | "Generate conventional commit messages from staged diffs" |
| 2 | Developers |
| 3 | Code generation |
| 4 | Read staged diff; use Conventional Commits format; keep subject ≤72 chars; include body for non-trivial changes |
| 5 | Never fabricate changes not in the diff; never use vague subjects like "update code" |
| 6 | "commit message, write commit, git commit, conventional commit" |
| 7 | Structured message |
| 8 | No companion files |
| 9 | Bash (for `git diff --staged`) |
| 10 | Generate scenarios |

**Scaffold produced:**

```
skills/commit-message/
├── SKILL.md          # Frontmatter with triggers, non-negotiables, format rules, integrated example
├── tile.json         # oh-my-ai/commit-message, v1.0.0
└── evals/
    ├── simple-feature-commit/
    │   ├── task.md        # "Given this staged diff adding a login form..."
    │   └── criteria.json  # Tests: conventional format, subject length, body presence
    └── noisy-multi-file-commit/
        ├── task.md        # "Given this large diff touching 8 files..."
        └── criteria.json  # Tests: focus, not fabricating, correct scope
```

**Repo integration:** README row added (alphabetically); CI matrix updated.

---

## Anti-patterns

- Generating SKILL.md before the interview is complete.
- Producing a skill with a description that lacks explicit trigger terms.
- Writing eval criteria that don't sum to 100.
- Proposing vague optimization suggestions instead of specific before/after edits.
- Modifying skill files during eval execution (`tessl eval run` / judge runs) or overwriting previous benchmark-log.md entries.
- Running `tessl skill review` in the middle of Phase 4, or guessing a scenario generation id instead of parsing CLI output.
- Skipping the lint check after scaffold generation, or inserting duplicate rows in README or the CI matrix.
- Adding or editing `skills-lock.json` when scaffolding a first-party skill under `skills/`.
