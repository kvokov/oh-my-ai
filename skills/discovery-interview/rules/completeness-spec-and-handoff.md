---
name: completeness-spec-and-handoff
description: Phases 5–7 — completeness gate, writing the spec from SPEC_TEMPLATE.md, and handoff
---

# Completeness check, spec output, and handoff

## Phase 5: Completeness check

Do **not** write the spec until all of the following are satisfied:

| Area | Must have |
|------|------------|
| **Problem** | Clear problem statement, success metrics, stakeholders |
| **UX** | User journey, core actions, error/edge handling |
| **Technical** | Data model, integrations, scale, security, deployment |
| **Decisions** | Tradeoffs explicitly chosen; no silent TBDs; user confirms understanding |

If something is missing: name the gap, run targeted Phase 2 questions, then **re-run this table**.

## Phase 6: Spec generation

1. Summarize understanding; get confirmation.
2. Generate `docs/specs/YYYY-MM-DD-<name>.md` from [SPEC_TEMPLATE.md](../SPEC_TEMPLATE.md).

## Phase 7: Implementation handoff

AskUserQuestion for next steps:

- **Start implementation** → User can say they want the spec implemented.
- **Plan implementation** → Plan agent or `/create_plan` if available.
- **Review spec / done** → Confirm path and that the spec reflects what was agreed.
