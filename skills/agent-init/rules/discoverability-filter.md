---
name: discoverability-filter
description: The core inclusion/exclusion logic for deciding what belongs in AGENTS.md — every candidate line must pass this filter before being included in the draft
---

# Discoverability Filter

The single most important principle of `AGENTS.md`: **if an agent can discover it by reading the repository, it does not belong in the file.**

## The test

For every candidate line, ask:

> Can an agent learn this by reading `README.md`, source code, config files, scripts, directory structure, linter rules, CI pipelines, tests, or type definitions?

- **Yes** → Exclude from `AGENTS.md`.
- **No**, and it materially affects task success, cost, or safety → Include.
- **No**, but it's trivial or cosmetic → Exclude.

## What passes the filter

A line earns inclusion when it is **all three**:

1. **Non-discoverable**: not present in or inferable from any repository file
2. **Operationally significant**: changes what commands to run, what files to touch, what output to produce, or what to avoid
3. **Actionable**: specific enough that an agent can execute on it without further research

### Concrete categories that typically pass

| Category | Why it's non-discoverable | Example |
|----------|--------------------------|---------|
| Command flags | Scripts hide the flags behind aliases or don't list them | `npm test -- --no-cache` (script just says `jest`) |
| Deprecated-but-imported code | No deprecation marker, still passes all checks | `packages/legacy-auth` imported in barrel, under migration |
| Environment variables not in .env.example | Agent can't guess them from code alone | `REDIS_URL` required but not documented |
| Order-sensitive operations | No script enforces the order | Must run `npx typeorm migration:run` before `npm run seed`, but no script encodes the order |
| CI quirks | Locally everything works, CI has a different behavior | CI uses a different Node version than `.nvmrc` suggests |
| Implicit conventions | No linter or test enforces them | PR titles must follow `type(scope): message` but no commitlint |
| Do-not-touch zones | Nothing marks them as off-limits in code | `config/production.yml` must only be edited by SRE team |

## What fails the filter

| Candidate | Why it fails | Discoverable from |
|-----------|-------------|-------------------|
| "This project uses TypeScript" | Stack summary | `tsconfig.json`, file extensions |
| "The `src/` directory contains source code" | Directory overview | Directory listing |
| "Use ESLint for linting" | Tooling description | `.eslintrc`, `package.json` devDeps |
| "Write meaningful variable names" | Generic advice | Not specific, not actionable |
| "Always run `npm install` first" | Standard practice | `package.json` existence |
| "Use Prettier for formatting" | Already enforced | `.prettierrc`, pre-commit hooks |
| "Tests are in the `__tests__` directory" | Inferable from structure | Directory listing, test config |
| "We use React for the frontend" | Stack summary | `package.json`, imports |

## Edge cases

**"But it's important!"** — Importance alone does not justify inclusion. If the information is in `README.md` or inferable from config, it is discoverable regardless of how critical it is. Agents read those files.

**Cross-file inference**: If understanding requires reading two files together in a non-obvious way, it may pass the filter. Example: README says "use yarn" but only `package-lock.json` exists — the contradiction is discoverable, but which one is *correct* is not.

**Stale documentation**: If `README.md` says X but the code does Y, the correct behavior may not be discoverable. Include the resolution in `AGENTS.md` and flag the stale docs for the user to fix.

**Multi-step implicit knowledge**: If correct execution requires knowing steps A → B → C in order, and no script encodes that order, the sequence is non-discoverable even if each individual step appears somewhere in the repo.

## Applying the filter during drafting

When generating `AGENTS.md` content:

1. Draft all candidate lines freely
2. For each line, identify the specific repo file an agent would read to learn this
3. If you can name the file and the relevant section → the line fails the filter → delete it
4. If you cannot → the line passes → keep it
5. After filtering, verify the remaining lines are actionable (specific command, specific file, specific behavior)

## Maintenance implication

Every line in `AGENTS.md` represents a gap between what the repo communicates and what agents need to know. The ideal state is an empty `AGENTS.md` — every instruction has been encoded into tooling, scripts, config, or documentation that agents can read directly. When pruning, ask: "Can this instruction be replaced by a lint rule, a script, a README section, or a config change?" If yes, recommend that fix instead of keeping the line.
