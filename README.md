# Oh My AI

Curated, open-source skills for AI coding agents. Each skill is a self-contained prompt and workflow that an agent loads to perform structured work — generating specs, auditing repos, building knowledge bases, and more.

Skills follow a simple convention: a directory with a `SKILL.md` file (the prompt) and optional supporting files. They work with any agent platform that supports the skill/prompt-loading pattern, including [Claude Code](https://docs.anthropic.com/en/docs/claude-code), [Tessl](https://docs.tessl.io), [Cursor](https://www.cursor.com), and others.

## Available Skills


| Skill                                              | What it does                                                                                                                                                                 |
| -------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [agent-init](skills/agent-init/)                   | Audits a repo and generates an `AGENTS.md` containing only non-discoverable instructions — commands, landmines, and conventions agents cannot infer from reading the code.   |
| [discovery-interview](skills/discovery-interview/) | Guided discovery interview that turns vague ideas into a structured spec: targeted Q&A, optional research, user stories, acceptance criteria, and constraints.               |
| [humaniser](skills/humaniser/)                     | Interactive de-AI rewrite: intake, voice calibration, pattern hunt, and audit pass so prose sounds human without inventing facts.                                         |
| [llm-wiki](skills/llm-wiki/)                       | Maintains a persistent markdown wiki between raw sources and answers — ingest, query, lint, index, and log — so knowledge compounds instead of being re-derived every query. |
| [reading-synthesis](skills/reading-synthesis/)     | Thirteen-part rigorous synthesis of articles, essays, chapters, or talks: deep summary, insights, structure, comparables, critique, framework, ten actions, ~300-word exec brief, and CEO-level stakes. |
| [skill-maker](skills/skill-maker/)                 | Interactive skill creation and eval-driven optimization: interview, scaffold, evaluate, refine. Produces complete skill directories ready for use.                           |


## Installation

### npx skills

The fastest way to install. Uses the [skills CLI](https://github.com/vercel-labs/skills):

```bash
# Install all skills from this repo
npx skills add kvokov/oh-my-ai

# Install a specific skill
npx skills add kvokov/oh-my-ai@agent-init
```

### Tessl

```bash
# Install from GitHub
tessl install github:kvokov/oh-my-ai

# Install a specific skill
tessl install github:kvokov/oh-my-ai --skill agent-init
```

Skills in this repo are also published as tiles. Each skill directory includes a `tile.json` for compatibility with `tessl tile lint` and `tessl tile publish`.

### Manual installation (any agent)

Clone the repo and copy the skill directories you want into your agent's skill path:

```bash
git clone https://github.com/kvokov/oh-my-ai.git
cp -r oh-my-ai/skills/agent-init .claude/skills/agent-init   # Claude Code
cp -r oh-my-ai/skills/agent-init .agents/skills/agent-init   # Other agents
```

Each skill is self-contained — copy the entire directory (including `SKILL.md` and any supporting files like templates or rules).

## Skill anatomy

```
skills/
  agent-init/
    SKILL.md          # The prompt — this is the skill
    tile.json         # Optional: Tessl tile metadata
    rules/            # Optional: supporting reference files
    evals/            # Optional: eval cases for testing
```

The only required file is `SKILL.md`. Everything else is optional and platform-specific.

## Contributing

1. Create `skills/<skill-name>/` with at minimum a `SKILL.md`.
2. Add a row to the table above.
3. Follow the structure and frontmatter conventions of existing skills (use `skills/agent-init/` as a reference).

## License

MIT — see [LICENSE](LICENSE).
