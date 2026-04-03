---
name: discovery-interview
description: "Conduct an interactive discovery interview to produce a structured product specification. Triggers: write a spec, PRD, feature spec, requirements, product requirements, scope a project, brainstorm a feature, flesh out an idea, plan a new project. Uses AskUserQuestion for all user choices; WebSearch/WebFetch when the user wants research. Outputs: user stories, acceptance criteria, technical constraints, prioritized requirements in docs/specs/ per SPEC_TEMPLATE.md. Do NOT use for: implementation, code review, debugging, refactors, or when the user already has a complete spec they only want edited."
---

# Discovery Interview

Turn vague ideas into an implementable spec through ordered phases and targeted questions.

## Non-negotiables (do not skip)

1. **Run all 7 phases in order** below. Do not jump to the spec early.
2. **Minimum 10–15 questions** across categories for any real project. **Never** treat 3–5 questions as enough.
3. **Every** AskUserQuestion must include an option for uncertainty (e.g. “I’m not sure” / “Research this”).
4. **At least one research loop** for any non-trivial project (user accepts research → WebSearch/WebFetch or agent, then follow-up questions).
5. **Completeness check (Phase 5) must pass** before writing the spec. If anything is missing, ask more; do not write the spec yet.
6. **Summarize your understanding** and get user confirmation **before** generating the spec file.
7. **Write the spec** to `docs/specs/YYYY-MM-DD-<name>.md` using [SPEC_TEMPLATE.md](./SPEC_TEMPLATE.md). Create `docs/specs/` if it does not exist.

## Process overview

Seven phases, in order.

### Phase 1: Initial orientation (2–3 questions)

Use AskUserQuestion to nail:

- What problem are you solving?
- Who will use this?
- New build vs improving something existing?

Infer **project type** (guides which categories matter most): Backend/API · Web · CLI · Mobile · Full-stack · Script/automation · Library/SDK.

### Phase 2: Category deep dive

Use [CATEGORIES.md](./CATEGORIES.md). For each **relevant** category:

1. Ask **at least 2** questions (typically **2–4**) via AskUserQuestion.
2. If the user is unsure, **offer research** (Phase 3 pattern).
3. **Educate** when needed so decisions are informed.
4. Track decisions internally.

### Phase 3: Research loops

On uncertainty or knowledge gaps, use AskUserQuestion first:

```
AskUserQuestion(
  question: "[Topic] has several viable approaches with different tradeoffs. Research?",
  options: [
    {label: "Yes, research", description: "Compare options and tradeoffs"},
    {label: "No", description: "I know what I want"},
    {label: "Brief only", description: "Short overview, no deep dive"}
  ]
)
```

If the user chooses research: WebSearch/WebFetch (or an agent), then **continue with informed questions**. Do not skip follow-up questions after research.

### Phase 4: Conflict resolution

When requirements conflict, surface the tension with AskUserQuestion. Options must include: favor constraint A, favor B, or explore alternatives (often with research).

**Example pattern** (latency vs cost — adapt the domain):

```
AskUserQuestion(
  question: "Tension: sub-second dashboard updates for everyone vs. smallest cloud tier. Real-time fan-out usually needs more than one small instance. Optimize for what first?",
  options: [
    {label: "Freshness", description: "Higher cost or fewer concurrent users for near-real-time"},
    {label: "Cost", description: "Slower refresh (polling/batch) on minimal infra"},
    {label: "Explore alternatives", description: "e.g. SSE + caching — research before choosing"}
  ]
)
```

Typical tensions: simple vs rich; real-time vs cheap infra; secure vs frictionless UX; flexible vs fast; ship fast vs future-proof.

### Phase 5: Completeness check

Do **not** write the spec until all of the following are satisfied:

| Area | Must have |
|------|------------|
| **Problem** | Clear problem statement, success metrics, stakeholders |
| **UX** | User journey, core actions, error/edge handling |
| **Technical** | Data model, integrations, scale, security, deployment |
| **Decisions** | Tradeoffs explicitly chosen; no silent TBDs; user confirms understanding |

If something is missing: name the gap, run targeted Phase 2 questions, then **re-run this table**.

### Phase 6: Spec generation

1. Summarize understanding; get confirmation.
2. Generate `docs/specs/YYYY-MM-DD-<name>.md` from [SPEC_TEMPLATE.md](./SPEC_TEMPLATE.md).

### Phase 7: Implementation handoff

AskUserQuestion for next steps:

- **Start implementation** → User can say they want the spec implemented.
- **Plan implementation** → Plan agent or `/create_plan` if available.
- **Review spec / done** → Confirm path and that the spec reflects what was agreed.

## Integrated flow (one pass)

Orientation → deep dive categories for the project type → if unsure on stack/approach, research loop → if two goals conflict, conflict resolution → completeness table green → confirm summary → write spec file → handoff question.

## Anti-patterns

- Writing the spec after a handful of questions.
- Skipping research when the user is guessing about tools or scale.
- Leaving contradictions unresolved.
- Writing the spec before the completeness check passes.
