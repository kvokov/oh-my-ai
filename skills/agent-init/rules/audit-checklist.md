---
name: audit-checklist
description: Systematic procedure for scanning a repository to build the repo profile that feeds gap detection and AGENTS.md drafting
---

# Audit Checklist

Run this checklist during Phase 1. The goal is to build a **repo profile** — an in-memory summary of what the repo communicates and where gaps exist. Do not output the profile to the user; it feeds Phase 2 (gap detection) directly.

## Scan order

Process sources in this order. Skip any that do not exist.

### 1. Existing agent configuration

Read these first — they tell you what's already covered (and what might be stale):

| File / path | What to extract |
|-------------|----------------|
| `AGENTS.md` | All current instructions. Flag each as: accurate, stale, or redundant. |
| `CLAUDE.md` | Claude-specific instructions. Note overlap with `AGENTS.md`. |
| `.cursor/rules/*.md` or `.cursorrules` | Cursor-specific rules. Note anything not in `AGENTS.md`. |
| `.github/copilot-instructions.md` | Copilot-specific guidance. Note anything not in `AGENTS.md`. |
| `GEMINI.md` | Gemini-specific instructions. Note anything not in `AGENTS.md`. |

Record: what instructions exist, which tool they target, and whether they're consistent across files.

### 2. Project documentation

| File | What to extract |
|------|----------------|
| `README.md` | Setup steps, project description, contribution guidelines, links |
| `PROJECT.md` | Architecture decisions, project context |
| `CONTRIBUTING.md` | PR process, review requirements, testing expectations |
| `CHANGELOG.md` | Recent changes that might indicate active migration or deprecation |

Record: what's documented (this is what agents can discover — it should NOT appear in `AGENTS.md`).

### 3. Package manager and dependencies

| File | What to extract |
|------|----------------|
| `package.json` | Scripts (especially non-obvious ones), `packageManager` field, workspaces config, engines |
| `package-lock.json` / `yarn.lock` / `pnpm-lock.yaml` / `bun.lockb` | Which lock file exists (determines actual package manager) |
| `pyproject.toml` / `setup.py` / `requirements.txt` | Python tooling, build system, dependency groups |
| `Cargo.toml` | Rust workspace, features, build profiles |
| `go.mod` | Go module path, Go version |
| `.tool-versions` / `.nvmrc` / `.python-version` / `rust-toolchain.toml` | Pinned runtime versions |

**Key check**: Does the lock file match what README/scripts say? A `package-lock.json` with a README that says "use yarn" is a common source of non-discoverable instructions.

Record: actual package manager, runtime versions, script names and what they do.

### 4. Build and task runners

| File | What to extract |
|------|----------------|
| `Makefile` | Targets, especially ones with non-obvious prerequisites |
| `Justfile` | Recipes with implicit ordering |
| `Taskfile.yml` | Task definitions and dependencies |
| `turbo.json` / `nx.json` | Monorepo task pipeline, caching behavior |

Record: multi-step commands, order-sensitive operations, caching quirks.

### 5. CI / CD pipelines

| File | What to extract |
|------|----------------|
| `.github/workflows/*.yml` | Jobs, matrix strategies, env vars, secrets referenced, special flags |
| `.gitlab-ci.yml` | Stages, variables, artifact paths |
| `Jenkinsfile` | Pipeline stages |
| `.circleci/config.yml` | Jobs, orbs, environment |

**Key check**: Do CI commands differ from local commands? (e.g., CI adds `--no-cache`, uses different env vars, runs additional steps). These differences are non-discoverable.

Record: CI-specific commands, environment differences, deployment triggers.

### 6. Linter and formatter configuration

| File | What to extract |
|------|----------------|
| `.eslintrc*` / `eslint.config.*` | Custom rules, overrides for specific directories |
| `.prettierrc*` / `.prettierignore` | Formatting scope |
| `biome.json` | Combined lint + format rules |
| `.rustfmt.toml` / `clippy.toml` | Rust-specific formatting and linting |
| `ruff.toml` / `pyproject.toml [tool.ruff]` | Python linting |
| `.editorconfig` | Editor-level formatting |
| `.pre-commit-config.yaml` | Pre-commit hooks (what's automatically enforced) |

Record: what's enforced automatically. These rules do NOT belong in `AGENTS.md` — they're already enforced.

### 7. Project configuration

| File | What to extract |
|------|----------------|
| `tsconfig*.json` | TypeScript settings, path aliases, strict mode |
| `next.config.*` / `nuxt.config.*` / `vite.config.*` | Framework config |
| `docker-compose*.yml` / `Dockerfile*` | Container setup, service dependencies |
| `.env.example` / `.env.template` | Documented environment variables |

**Key check**: Are there env vars used in code that are NOT in `.env.example`? Scan source files for `process.env.`, `os.environ`, `env::var`, `std::env` patterns and compare against `.env.example`.

Record: documented vs. undocumented env vars, service dependencies.

### 8. Directory structure scan

Run a quick directory listing (top 2-3 levels). Look for:

- **Deprecated-looking directories** still imported somewhere (check barrel files, `index.*` re-exports)
- **Unconventional naming** that might confuse agents (e.g., `src/` vs `lib/` vs `app/`)
- **Module boundaries** in monorepos (each top-level package might warrant its own `AGENTS.md`)

Record: module count (for hierarchical AGENTS.md recommendation), suspicious directories.

## Building the repo profile

After scanning, compile findings into these categories:

```
repo_profile:
  stack:
    languages: [...]
    frameworks: [...]
    package_manager: <actual, from lock file>
    runtime_versions: <from version files>

  existing_agent_instructions:
    files_found: [...]
    total_instructions: N
    stale: [<instruction, reason>]
    redundant: [<instruction, discoverable_from>]
    valid: [<instruction>]

  gaps:
    commands: [<non-obvious command + why>]
    landmines: [<hidden constraint + where found>]
    conventions: [<unforced convention + evidence>]
    env_vars: [<undocumented var + files that use it>]
    routing: [<module-specific guidance needed>]

  ambiguities: [<conflicting signal + what to ask user>]
```

This profile is consumed by Phase 2 (gap detection). Do not output it to the user.

## Scan performance

For large repositories (1000+ files), prioritize:

1. Root-level config files (always read)
2. CI and workflow files (always read)
3. Top-level directory structure (always scan)
4. Source file sampling for env var detection (grep, don't read every file)

Do not read every source file. Use `Grep` for targeted searches (env vars, imports of suspicious directories, barrel file re-exports).
