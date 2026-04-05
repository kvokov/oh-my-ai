# Agent instructions — Oh My Skills

Non-discoverable constraints: things that are easy to miss if you only read `README.md` or a single skill without checking layout, lockfiles, or CI together.

## `skills/` vs `.agents/skills/`

- `**skills/<name>/**`: First-party tiles published from this repo (`README.md` table + Tessl on `main` for each directory that contains `tile.json`; CI discovers tiles automatically).
- `**.agents/skills/**`: Vendored copies pinned in `skills-lock.json` at the repo root. Used for editor/agent workflows here; not the same set as `skills/`. Nothing under `.agents/skills/` is published by this repo’s workflow unless you also ship a matching tile under `skills/` and wire CI.

## Adding a first-party skill under `skills/`

1. Add a row to the table in `README.md`.
2. Create `skills/<skill-name>/` with `SKILL.md` and `**tile.json**` (Tessl’s working directory per job is the tile path; lint/publish expect that package layout). Use `skills/discovery-interview/` as the reference for frontmatter, tone, and `tile.json` shape.
3. Ensure `tile.json` exists at `skills/<skill-name>/tile.json`. On pushes to `main`, CI runs `tessl tile lint` on every discovered tile and `tessl tile publish` only for tiles whose files changed in that push.

## Editing skills

Match existing skills’ structure and frontmatter. If a skill references tools (e.g. `AskUserQuestion`), keep names aligned with how agents in this workspace actually run.