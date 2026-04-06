# Agent instructions — Oh My AI

Non-discoverable constraints: things that are easy to miss if you only read `README.md` or a single skill without checking layout, lockfiles, or CI together.

## Published skills (`skills/`)

- **`skills/<name>/`** is the canonical source of truth for all skills. The `README.md` table must stay in sync with what exists under `skills/`.
- Each skill directory must contain a `SKILL.md`. A `tile.json` is optional (used for Tessl tile publishing via CI).
- CI on `main` discovers tiles via `tile.json`, runs `tessl tile lint` on all of them, and runs `tessl tile publish` only for tiles whose files changed in that push.

## Local maintainer tooling (optional)

These paths exist only for local development and are not part of the shared repo contract. Do not rely on them as the canonical copy of skills — use **`skills/`** for that.

- **`skills-lock.json`** (repo root)
- **`.agents/skills/<name>/`**
- **`.claude/skills/<name>/`** (may symlink to `.agents/skills/<name>/`)

## Adding a skill

1. Create `skills/<skill-name>/` with at minimum a `SKILL.md`.
2. Add a row to the table in `README.md`.
3. Use `skills/agent-init/` as the reference for frontmatter, tone, and directory layout.
4. If you want the skill published as a Tessl tile, add a `tile.json` (see existing tiles for the shape).

## Editing skills

Match existing skills’ structure and frontmatter. If a skill references tools (e.g. `AskUserQuestion`), keep names aligned with how agents in this workspace actually run.