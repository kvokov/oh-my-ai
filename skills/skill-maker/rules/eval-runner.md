---
name: eval-runner
description: How to run skill evaluations via Tessl CLI or LLM-as-judge and normalize results
---

# Eval runner (skill-maker reference)

Eval scenarios live under `skills/<skill-name>/evals/<scenario-slug>/` as `task.md` + `criteria.json`. Scenario shape and weighting follow [benchmark-loop](./benchmark-loop.md).

**Constraint:** Evaluation is **read-only** for skill sources. Do not edit SKILL.md, tile.json, or rules during eval execution. Changes happen only in the optimizer apply step after user approval.

## Path A — Tessl CLI (preferred)

1. Detect CLI: `which tessl` (or equivalent).
2. Run from repo context:

   ```bash
   tessl eval run skills/<skill-name> --json
   ```

3. Parse JSON into a normalized result (see **Unified schema** below). Surface per-scenario totals and per-criterion scores if present in the CLI output.

4. If the command fails, capture stderr, report to the user, and consider Path B if appropriate.

## Path B — LLM-as-judge fallback

Use when Tessl is missing, eval run fails, or the user explicitly wants judge-only scoring.

For **each** scenario:

1. Read `evals/<slug>/task.md` as the user task prompt.
2. Read `evals/<slug>/criteria.json` (weighted checklist; scores sum to 100).
3. **Baseline run:** Subagent receives only the task (no skill file). Record full output.
4. **With-skill run:** Subagent receives the task with `SKILL.md` content prepended or clearly labeled as system/skill context. Record full output.
5. **Per criterion:** Invoke a judge step (can be same or separate subagent) with:
   - Criterion `name`, `max_score`, `description`
   - The output being scored (baseline or with-skill as required)
   - Ask for strict JSON: `{"score": <number 0..max_score>, "reasoning": "<brief>"}`

Aggregate scenario score as the sum of criterion scores (should align with checklist weights totaling 100).

Repeat for baseline and with-skill to compute **delta** (with-skill total minus baseline total) per scenario.

## Unified result schema

Normalize both paths to:

| Field | Meaning |
|-------|---------|
| `date` | ISO-8601 timestamp when the run completed |
| `method` | `tessl-cli` or `llm-as-judge` |
| `model` | Model id used for agent/judge where applicable |
| `scenarios` | Array of `{ "slug", "baseline", "withSkill", "delta", "criteria": [{ "name", "score", "max", ... }] }` |

Per-criterion breakdown may be partial for Path A depending on CLI JSON shape; preserve whatever the tool returns.

## Calibration (when both paths run)

If Path A and Path B both succeed on the same scenarios:

- Compare totals per scenario.
- Treat **±15%** relative difference as acceptable (per product spec); document both in benchmark-log narrative if diverging.
- Prefer **Path A** scores for optimization decisions when they disagree beyond tolerance.

## Handoff to Phase 5–6

- Feed normalized results into the optimizer (Phase 5) for failure classification.
- Append a human-readable run block to `skills/<skill-name>/benchmark-log.md` (Phase 6); see main SKILL.md for table format and gate checks.
