---
name: humaniser
description: "Remove AI-writing tells and rewrite prose to sound natural and human. Triggers: humanise, humanize, de-AI, deAI, sound natural, less robotic, remove AI tells, AI writing, chatbot tone, Wikipedia AI writing signs, voice match, voice calibration. Uses AskUserQuestion for intake; Read/Write for files; WebSearch/WebFetch when the user wants terminology or usage research; Bash when applying edits in a repo. Outputs: interactive Q&A then rewritten text (and optional brief edit log). Do NOT use for: inventing facts, changing the author's stance, weakening safety or compliance language without explicit instruction, or skipping intake when the user's goals and preservation rules are unclear."
metadata:
  version: "1.0.1"
  tags: writing, editing, de-ai, voice, prose, humanise, natural language
---

# Humaniser

Strip generic "model voice" from text: fewer stock phrases and signposts, more direct sentences, and (when requested) a closer match to the user's own writing. The aim is **human-sounding text without new false specifics**.

## Non-negotiables (do not skip)

1. **Run interactive intake first** using [interactive-intake](./rules/interactive-intake.md). Do not rewrite until goal, register, preservation rules, and uncertainty handling are settled. Every AskUserQuestion set must include an **uncertainty** option (e.g. "I'm not sure — help me decide").
2. **Preserve truth and stance.** Do not invent statistics, quotes, citations, names, dates, or product facts. Do not reverse or soften the author's position unless explicitly asked.
3. **Apply the hunt list** from [ai-tells-and-patterns](./rules/ai-tells-and-patterns.md) during the first rewrite and again in the audit pass.
4. **Voice calibration:** when the user supplies a writing sample, match its rhythm and diction; do not replace it with a generic "clean" voice.
5. **Do a second pass** after the first rewrite: re-read only for lingering AI tells (signposting, em dash clusters, promotional adjectives, chatbot wrappers). Fix what still sounds synthetic; if the first pass already reads human, keep edits minimal.
6. **Deliver clearly.** Default output is the rewritten text. If the user asks how it changed, add a **short** bullet log (max 5–8 bullets) of categories touched — not a line-by-line diff unless requested.
7. **Never** add "helpful assistant" preambles or closers to the user's content unless they asked for a conversational reply scaffold.

## Process

### Phase 1 — Intake (AskUserQuestion)

Follow [interactive-intake](./rules/interactive-intake.md): goal, register, preservation, locale/format, whether to strip chatbot wrappers. If the user is unsure on register or locale, propose 2–3 concrete defaults and let them pick.

### Phase 2 — First rewrite

Rewrite the body using [ai-tells-and-patterns](./rules/ai-tells-and-patterns.md):

- Cut or replace **significance inflation** and vague attributions.
- Replace **AI vocabulary clusters** with plain verbs and nouns.
- Fix **style tells**: em dash pile-ups, decorative bold/emoji, title-case shouting, signposting openers.
- Remove **chatbot artifacts** when Round 5 says to strip them.

Keep structure unless the user chose a heavier rewrite; when in doubt, preserve paragraph order.

### Phase 3 — Audit pass ("still sounds like a brochure?")

Read the rewritten text once **as skeptic**: any remaining stock phrase, triple parallelism, or anonymous authority? Tighten. If a sentence is clear and human, leave it alone.

### Phase 4 — Deliver

Return the final text first. Offer the optional short edit log only on request or when the user is learning what "de-AI" means.

## Working from files

When the source lives in the repo:

1. **Read** the file, run intake (the user may answer about path and constraints in chat).
2. **Write** the updated content back with minimal unrelated churn — same formatting conventions as the file unless the user asked for structural changes.
3. Use **Bash** only for repo operations the user requested (e.g. formatters); do not run destructive commands without explicit confirmation.

## Integrated example

**User paste (AI-sounding draft):**

> Great question! In today's rapidly evolving landscape, AI-assisted coding serves as a transformative testament to innovation—underscoring its pivotal role across the industry. Experts agree that organizations must align on best practices in order to unlock synergies. While challenges remain, the future looks bright.

**Intake (abbreviated):** AskUserQuestion rounds establish: email to a skeptical senior engineer, conversational-but-professional register, preserve no numbers (there are none), strip chatbot wrappers, US English.

**After Phase 2–3 (humanised):**

> AI coding tools are everywhere now. They're best for boilerplate and first drafts; they're risky when the suggestion looks right but isn't. The hard part isn't writing faster—it's catching wrong assumptions before they ship. If your team doesn't have tests and review habits in place, "faster" mostly means "bugs sooner."

This example applies non-negotiables **#3** (hunt list), **#5** (audit pass), and **#7** (no assistant preamble added).

## Anti-patterns

- Rewriting before asking what must stay verbatim (names, claims, legal language).
- Swapping AI clichés for **new** clichés ("at the end of the day", "north star").
- Adding fake specificity ("a 2021 study…") to sound credible.
- Turning everything into jokes or extreme casualness when professional tone was requested.
- Dumping a 20-bullet change log unless the user asked for it.

## Related references

- [Wikipedia: Signs of AI writing](https://en.wikipedia.org/wiki/Wikipedia:Signs_of_AI_writing) — pattern taxonomy and community maintenance notes.
