---
name: discovery-interview
description: "Conduct an interactive discovery interview to produce a structured product specification. Triggers: write a spec, PRD, feature spec, requirements, product requirements, scope a project, brainstorm a feature, flesh out an idea, plan a new project. Uses AskUserQuestion for all user choices; WebSearch/WebFetch when the user wants research. Outputs: user stories, acceptance criteria, technical constraints, prioritized requirements in docs/specs/ per SPEC_TEMPLATE.md. Do NOT use for: implementation, code review, debugging, refactors, or when the user already has a complete spec they only want edited."
metadata:
  tags: discovery, product-spec, PRD, requirements, AskUserQuestion, interviews, specs
---

# Discovery Interview

Turn vague ideas into an implementable spec through ordered phases and targeted questions.

## Non-negotiables (do not skip)

1. **Run all 7 phases in order** below. Do not jump to the spec early.
2. **Minimum 10–15 questions** across categories for any real project. **Never** treat 3–5 questions as enough.
3. **Every** AskUserQuestion must include an option for uncertainty (e.g. "I'm not sure" / "Research this").

   *Example AskUserQuestion call:*
   ```
   AskUserQuestion(
     question: "Which database are you planning to use?",
     options: ["PostgreSQL", "MySQL", "MongoDB", "I'm not sure", "Research this for me"]
   )
   ```

4. **At least one research loop** for any non-trivial project (user accepts research → WebSearch/WebFetch or agent, then follow-up questions).

   *Example research loop:* User selects "Research this for me" on a scaling question → `WebSearch("serverless vs container hosting cost comparison 2024")` + `WebFetch(top result)` → follow-up: "Based on the research, AWS Lambda looks cost-effective at your expected traffic. Does that align with your budget?"

5. **Completeness check (Phase 5) must pass** before writing the spec. If anything is missing, ask more; do not write the spec yet.
6. **Summarize your understanding** and get user confirmation **before** generating the spec file.
7. **Write the spec** to `docs/specs/YYYY-MM-DD-<name>.md` using [SPEC_TEMPLATE.md](./SPEC_TEMPLATE.md). Create `docs/specs/` if it does not exist.

## Process

Execute phases **in order**. Detailed steps live in companion rules:

| Phase | Focus | Rules |
|-------|--------|--------|
| 1 | Initial orientation | [orientation-and-deep-dive](./rules/orientation-and-deep-dive.md#phase-1-initial-orientation) |
| 2 | Category deep dive | [orientation-and-deep-dive](./rules/orientation-and-deep-dive.md#phase-2-category-deep-dive) |
| 3 | Research loops | [research-and-conflict-resolution](./rules/research-and-conflict-resolution.md#phase-3-research-loops) |
| 4 | Conflict resolution | [research-and-conflict-resolution](./rules/research-and-conflict-resolution.md#phase-4-conflict-resolution) |
| 5 | Completeness check | [completeness-spec-and-handoff](./rules/completeness-spec-and-handoff.md#phase-5-completeness-check) |
| 6 | Spec generation | [completeness-spec-and-handoff](./rules/completeness-spec-and-handoff.md#phase-6-spec-generation) |
| 7 | Implementation handoff | [completeness-spec-and-handoff](./rules/completeness-spec-and-handoff.md#phase-7-implementation-handoff) |

## Integrated flow (one pass)

Orientation → deep dive categories for the project type → if unsure on stack/approach, research loop → if two goals conflict, conflict resolution → completeness table green → confirm summary → write spec file → handoff question.

## Anti-patterns

- Writing the spec after a handful of questions.
- Skipping research when the user is guessing about tools or scale.
- Leaving contradictions unresolved.
- Writing the spec before the completeness check passes.
