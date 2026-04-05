---
name: activation-design
description: Patterns for designing skills that activate reliably and instructions that stick
---

# Activation Design (skill-maker reference)

Use these patterns when generating SKILL.md frontmatter and body content.

## Trigger design

List concrete user/task signals near the top of the description field:

- Domain nouns specific to the skill's purpose (e.g. "CSV", "OAuth", "Fastify plugin")
- Failure cues the skill addresses (e.g. "timeouts", "flaky tests", "backpressure")
- Output shape cues (e.g. "commit footer", "schema file", "endpoint spec")

Format the description field as: purpose sentence → "Triggers: ..." → "Uses: ..." → "Outputs: ..." → "Do NOT use for: ..."

## Non-negotiable placement

If a behavior is frequently missed, move it into a numbered top-level checklist with imperative wording:

- "Use `pipeline(...)`" not "consider using pipeline"
- "Include `Refs:` when requested" not "you may want to add Refs"
- "Do not omit ..." not "try to avoid omitting"

## Integrated examples

Every SKILL.md should include at least one example that:

- Starts from realistic input (not a toy scenario)
- Includes critical decision points the skill must handle
- Shows the final output format

## Wording strictness

Replace fuzzy phrasing with strict phrasing when correctness matters:

| Avoid | Use instead |
|-------|-------------|
| "consider using" | "use" |
| "you may want" | "must include" |
| "try to" | "do" |
| "optionally" (for required behaviors) | "always" |

## Anti-patterns to flag during scaffold review

- Critical rules buried deep in long files
- One core behavior split across too many companion documents
- Examples that are toy-only and don't reflect real prompts
- Description field that doesn't contain explicit trigger terms
