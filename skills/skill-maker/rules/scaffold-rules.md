---
name: scaffold-rules
description: How to turn an interview decision map into a lint-passing skill directory and wire the repo
---

# Scaffold rules (skill-maker reference)

Use after Phase 1 (interview) completes and the decision map passes the completeness check. Cross-check generated SKILL.md content with [activation-design](./activation-design.md).

## Inputs

- **Decision map:** In-memory structure from the 10 interview answers (purpose, audience, domain, behaviors, anti-patterns, triggers, output shape, companion files, tools, eval ideas).

## Outputs (target tree)

```
skills/<skill-name>/
├── SKILL.md
├── tile.json
├── rules/              # optional
│   └── *.md
├── evals/              # optional; often added in Phase 3
│   └── <slug>/
│       ├── task.md
│       └── criteria.json
└── benchmark-log.md    # created when evals run (Phase 6)
```

## SKILL.md

### Frontmatter

```yaml
---
name: <skill-name>
description: "<Purpose>. Triggers: <trigger terms>. Uses <tools>. Outputs: <deliverables>. Do NOT use for: <exclusions>."
metadata:
  version: "1.0.0"
  tags: <domain tags>
---
```

- **Description:** Single string; front-load concrete trigger terms (nouns, failure cues, output-shape cues). Follow activation-design: purpose → Triggers → Uses → Outputs → Do NOT use for.
- **`metadata.version`:** Required on every SKILL.md — semver string (`MAJOR.MINOR.PATCH`). New skills start at `1.0.0`; bump when behavior or user-facing guidance changes materially (patch for small clarifications, minor for new sections or workflow changes, major for breaking rewrites).

### Body

1. Title + one-line value proposition.
2. **Non-negotiables:** Numbered checklist, imperative voice only ("Use …", "Do not …", "Always …"). No "consider", "may want", "try to".
3. **Process / phases** appropriate to the domain archetype.
4. **Integrated example:** Realistic input → decision points → output; must exercise at least two non-negotiables.
5. **Anti-patterns** (what the skill must avoid).

**Length:** Target 150–400 lines. If longer, move secondary material into `rules/<name>.md` with YAML frontmatter (`name`, `description`) and link from SKILL.md.

## tile.json

Exact shape:

```json
{
  "name": "oh-my-ai/<skill-name>",
  "version": "1.0.0",
  "private": false,
  "summary": "<one-line purpose>",
  "skills": {
    "<skill-name>": { "path": "SKILL.md" }
  }
}
```

- `name` must be `oh-my-ai/<skill-name>` matching the directory name under `skills/`.

## Companion files (conditional)

| Need | Action |
|------|--------|
| Rules split | `rules/<rule-name>.md` with frontmatter `name`, `description`; link from SKILL.md with relative paths. |
| Templates | Files at skill root or subdirs; reference from SKILL.md. |

## Repo integration (mandatory, idempotent)

**Do not** add or edit `skills-lock.json`. That file pins vendored copies under `.agents/skills/`. First-party tiles under `skills/` are registered only via README and CI.

1. **README.md** — Find the `| Skill | Description |` table. Insert a new row **in alphabetical order** by skill name. Skip if `skills/<skill-name>/` is already listed.
2. **CI** — `.github/workflows/tessl-publish.yml`: under `matrix.tile`, append `- skills/<skill-name>` if not already present.

After editing the workflow file, validate YAML parses, e.g.:

```bash
python3 -c "import yaml; yaml.safe_load(open('.github/workflows/tessl-publish.yml'))"
```

If validation fails, revert the CI edit and report the error.

## Lint check

**Preferred:** From repo root or tile directory:

```bash
cd skills/<skill-name> && tessl tile lint
```

**If `tessl` is unavailable — simulated checks:**

- SKILL.md: valid YAML frontmatter; `name`, `description`, and `metadata.version` present.
- tile.json: `name`, `version`, `summary`, `skills` present; `name` equals `oh-my-ai/<skill-name>`.
- All relative links from SKILL.md resolve.
- Every `rules/*.md` has YAML frontmatter with `name` and `description`.

Fix failures before handing off the scaffold.
