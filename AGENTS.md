# Agent instructions — Oh My Skills

Non-discoverable constraints: things that are easy to miss if you only read `README.md` or a single skill without checking layout, lockfiles, or CI together.

## Published tiles (`skills/`)

- **`skills/<name>/`** is the source of truth for first-party tiles: `README.md` table, `tile.json` per tile directory, and CI on `main` that discovers tiles, runs `tessl tile lint`, and publishes tiles whose files changed.

## Local maintainer tooling (optional)

These paths exist only for local development by the maintainer and are not part of the shared repo contract; they may be added to `.gitignore` later. Do not rely on them as the canonical copy of skills — use **`skills/`** for that.

- **`skills-lock.json`** (repo root)
- **`.agents/skills/<name>/`**
- **`.claude/skills/<name>/`** (may symlink to `.agents/skills/<name>/`)

## Adding a first-party skill under `skills/`

1. Add a row to the table in `README.md`.
2. Create `skills/<skill-name>/` with `SKILL.md` and `tile.json` (Tessl’s working directory per job is the tile path; lint/publish expect that package layout). Use `skills/agent-init/` as the reference for frontmatter, tone, and `tile.json` shape.
3. Ensure `tile.json` exists at `skills/<skill-name>/tile.json`. On pushes to `main`, CI runs `tessl tile lint` on every discovered tile and `tessl tile publish` only for tiles whose files changed in that push.

## Editing skills

Match existing skills’ structure and frontmatter. If a skill references tools (e.g. `AskUserQuestion`), keep names aligned with how agents in this workspace actually run.