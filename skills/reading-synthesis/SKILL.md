---
name: reading-synthesis
description: "Rigorous thirteen-part synthesis of a text or talk: deep summary, insights, structure, critique, framework rebuild, and CEO-level takeaways. Triggers: reading synthesis, synthesize this, deep dive, rigorous analysis, deconstruct, book analysis, article analysis, essay breakdown, intellectual synthesis, multi-dimensional analysis, executive summary of ideas, framework extraction. Uses: Read (and related file tools) for attached sources; WebSearch or WebFetch when comparands are missing or context is thin. Outputs: single structured markdown message with fixed section headers per rules/output-sections.md."
metadata:
  version: "1.0.0"
  tags: reading, synthesis, analysis, nonfiction, fiction, mental-models, critique, executive-summary
---

# Reading synthesis

Turn a supplied title, excerpt, file, or full work into one cohesive, high-value analysis aimed at **semi-technical** readers who want depth without academic padding.

## Non-negotiables

1. **Use the full rubric** in [output-sections](./rules/output-sections.md): produce **all thirteen** sections with the **exact** headers and order defined there, unless the user explicitly limits scope in the same turn.
2. **Ingest the whole artifact** when the user provides a path or attachment: read the complete source with file tools before analyzing. If only a fragment is given, analyze that fragment and **state what is missing** for full-work claims.
3. **Ban fluff and repetition**: no throat-clearing, no duplicate sentences across sections, no vague praise. Every paragraph must earn its place.
4. **Keep section 8 near 300 words** (280–320). Count mentally; tighten if over.
5. **Deliver exactly ten numbered actions** in section 10; each must be specific and schedulable.
6. **Comparative section (5) requires 3–5 named works or thinkers** in the same territory. If the user names none, run **WebSearch** (or WebFetch on solid results) to propose defensible comparands. Never imply you read books you did not access; if comparison is based on well-known summaries, label uncertainty.
7. **Label inference**: in mindset and decision lenses, separate **text-evidenced** claims from **reasonable inference** from tone, structure, or context.
8. **Format**: professional, direct tone; level-2 headers matching the rubric file; optional brief bullets inside sections where density helps.

## Process

1. **Clarify the artifact** — title, medium (essay, book chapter, talk transcript, etc.), and whether the user wants the full rubric or a subset. If they are silent, run the full rubric.
2. **Load content** — from message or via Read/Glob as needed. Note length limits: if the source is huge, analyze the portion provided or offer to continue in a follow-up with explicit scope.
3. **Classify structure** — fiction vs nonfiction (or hybrid); pick the appropriate structural template in section 6 of the rubric.
4. **Research comparands** if needed for section 5 — short, targeted queries; prefer stable, citable names and works.
5. **Draft all thirteen sections in one pass** — maintain consistent terminology; cross-reference themes instead of copying paragraphs.
6. **Self-check before send** — headers match rubric; section 8 word band; section 10 has ten items; no empty critique; Socratic Q&A is ordered logically.

## Integrated example (abbreviated shape)

**User:** “Here’s a blog post on compound interest and career capital [paste].”

**You:** Read the paste → section 6 uses nonfiction outline → section 5 compares to similar career-design writing (names 3–4 real essays/books via search if needed) → section 8 stays ~300 words with no repeat of section 1 → section 9 lists principles as a system → section 10 lists ten actions (e.g. “Schedule 90 minutes weekly for skill stack review”).

## Anti-patterns

- Inventing quotations, studies, or comparands that cannot be traced to the source or to named public works.
- Replacing critique with tone policing or generic “some may disagree.”
- Turning the Socratic skeleton into a FAQ unrelated to the work’s through-line.
- Using the CEO section for a full recap of earlier sections instead of **stakes, decisions, and behavior change**.
