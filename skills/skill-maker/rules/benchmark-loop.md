---
name: benchmark-loop
description: Process for structuring evals, interpreting results, and driving optimization
---

# Benchmark Loop (skill-maker reference)

Use these patterns when generating eval scenarios and analyzing eval results.

## Minimum eval matrix

For each scenario, capture:

- Overall score (sum of weighted criteria)
- Per-criterion score (0 to max_score)
- Delta: with-skill score minus baseline score

## Required scenario coverage

Every skill should have:

- One scenario per core capability the skill claims
- One scenario that stresses omission-prone outputs (footers, checklists, required sections)
- One scenario with noisy context to test retrieval under pressure

## Criteria weighting

Scores in criteria.json must sum to 100. Target distribution:

- Core behaviors: 60–70% of total weight
- Anti-pattern avoidance: 15–20% of total weight
- Output format compliance: 10–20% of total weight

## Result interpretation

| Pattern | Signal | Action |
|---------|--------|--------|
| High baseline + tiny delta | Model already knows this | Reduce verbosity or specialize edge-cases |
| Low baseline + high delta | Skill adds strong value | Preserve and refine |
| Low baseline + low with-skill | Skill content is weak or unclear | Rewrite instructions |
| Negative delta | Skill introduces confusion | Patch immediately |

## Readout format

Use this table structure in benchmark-log.md:

```md
## Run: <ISO-8601 timestamp>

**Method:** tessl-cli | llm-as-judge | **Model:** <model-name>

| Scenario | Baseline | With Skill | Delta |
|----------|----------|------------|-------|
| ...      | ...      | ...        | ...   |

**Changes applied:** <summary of edits made before this run>
```

## Optimization priorities

1. Fix negative deltas first (regressions)
2. Then fix 0% criteria with skill enabled (universal failures)
3. Then improve lowest-delta scenarios
4. Then improve lowest-scoring individual criteria
