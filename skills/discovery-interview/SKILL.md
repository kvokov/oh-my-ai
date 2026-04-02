---
name: discovery-interview
description: "Conduct an interactive discovery interview to produce a structured product specification. Use when the user wants to: write a spec, define requirements, create a PRD, flesh out an idea, brainstorm a feature, plan a new project, write a feature spec, define product requirements, or scope a technical project. Asks targeted clarifying questions through AskUserQuestion, identifies knowledge gaps and edge cases, researches tradeoffs when uncertainty exists, and outputs a structured specification document with user stories, acceptance criteria, technical constraints, and prioritized requirements. Do NOT use for implementation, code review, debugging, or tasks where the user already has a complete spec."
---

# Discovery Interview

Transform vague ideas into detailed, implementable specifications through iterative Q&A.

## Process Overview

7 phases, executed in order. Do not skip phases. Minimum 10-15 questions across categories for any real project.

### Phase 1: Initial Orientation (2-3 questions max)

Use AskUserQuestion to understand the shape of the idea:
- What problem are you trying to solve?
- Who will use this?
- New thing or improving something existing?

Determine PROJECT TYPE from answers:
- **Backend service/API** → Focus: data, scaling, integrations
- **Frontend/Web app** → Focus: UX, state, responsiveness
- **CLI tool** → Focus: ergonomics, composability, output formats
- **Mobile app** → Focus: offline, platform, permissions
- **Full-stack app** → Focus: all of the above
- **Script/Automation** → Focus: triggers, reliability, idempotency
- **Library/SDK** → Focus: API design, docs, versioning

### Phase 2: Category Deep Dive

Work through relevant categories from CATEGORIES.md. For each:
1. Ask 2-4 questions using AskUserQuestion
2. Detect uncertainty — if user seems unsure, offer research
3. Educate when needed — don't let them make uninformed decisions
4. Track decisions internally

Always include an "I'm not sure" or "Research this" option in AskUserQuestion to acknowledge uncertainty.

### Phase 3: Research Loops

When you detect uncertainty or knowledge gaps, offer to research:

```
AskUserQuestion(
  question: "You mentioned [X]. There are several approaches with different tradeoffs. Want me to research this?",
  options: [
    {label: "Yes, research it", description: "I'll investigate options and explain tradeoffs"},
    {label: "No, I know what I want", description: "Skip research"},
    {label: "Tell me briefly", description: "Quick overview without deep research"}
  ]
)
```

If user wants research: use WebSearch/WebFetch or spawn an agent, then return with informed follow-up questions.

### Phase 4: Conflict Resolution

When you discover conflicting requirements, surface them explicitly via AskUserQuestion with options to prioritize one side, the other, or explore alternatives.

Common conflicts: simple vs. feature-rich, real-time vs. cheap infrastructure, highly secure vs. frictionless UX, flexible vs. performant, fast to build vs. future-proof.

### Phase 5: Completeness Check

Before writing the spec, verify you have:

**Problem Definition:** Clear problem statement, success metrics, stakeholders identified.

**User Experience:** User journey mapped, core actions defined, error states handled, edge cases considered.

**Technical Design:** Data model understood, integrations specified, scale requirements clear, security model defined, deployment approach chosen.

**Decisions:** All tradeoffs explicitly chosen, no TBD items, user confirmed understanding.

If anything is missing, go back and ask more questions.

### Phase 6: Spec Generation

1. **Summarize understanding** and ask user to confirm before writing.
2. **Generate the spec** to `docs/specs/YYYY-MM-DD-<name>.md` using the template in SPEC_TEMPLATE.md.

### Phase 7: Implementation Handoff

Ask about next steps via AskUserQuestion:
- **Start implementation now** → Tell user to say "implement the <name> spec"
- **Plan implementation** → Spawn plan agent or invoke /create_plan
- **Review spec first / Done for now** → Confirm spec location and contents

## Iteration Rules

1. Never write the spec after just 3-5 questions — that produces incomplete specs
2. Minimum 10-15 questions across categories for any real project
3. At least 2 questions per relevant category
4. At least 1 research loop for any non-trivial project
5. Always do the completeness check before writing
6. Always summarize understanding before finalizing
