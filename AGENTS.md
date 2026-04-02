# Agent instructions — Oh My Skills

This repository holds **agent skills**: reusable instruction sets for AI-assisted development. Treat skills as the product; keep them focused, accurate, and easy for an agent to follow end-to-end.

## Layout


| Path                                   | Purpose                                                                             |
| -------------------------------------- | ----------------------------------------------------------------------------------- |
| `skills/<skill-name>/`                 | One skill per directory. Primary entry is `SKILL.md`.                               |
| `skills/<skill-name>/` (support files) | Optional: templates, category lists, assets referenced from `SKILL.md`.             |
| `README.md`                            | Human-facing index of available skills — update it when adding or renaming a skill. |


## Skill format (`SKILL.md`)

- Start with YAML frontmatter:
  - `name`: short identifier (usually matches the directory name).
  - `description`: when to use the skill and what it does; include trigger phrases so routing is reliable.
- Body: clear steps, phases, or sections. Prefer explicit “do / don’t” and ordering over vague prose.
- Link or reference companion files (e.g. `CATEGORIES.md`, `SPEC_TEMPLATE.md`) instead of duplicating large templates inline.

## When editing or adding skills

- **Scope**: Change only what the task requires; do not refactor unrelated skills or repo-wide structure unless asked.
- **Consistency**: Match the tone, structure, and frontmatter style of existing skills (see `skills/discovery-interview/SKILL.md`).
- **Discoverability**: If you add a new top-level skill, add a row to the table in `README.md`.
- **Accuracy**: If a skill references tools (e.g. `AskUserQuestion`, web search), keep names and behavior aligned with how this workspace’s agents actually run.

## License

Project license is in `LICENSE` (MIT).