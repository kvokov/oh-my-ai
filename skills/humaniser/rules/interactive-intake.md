---
name: interactive-intake
description: Mandatory AskUserQuestion rounds before rewriting — audience, constraints, voice, and preservation rules
---

# Interactive intake (before any rewrite)

Run these rounds **before** you edit the user’s text. Combine questions when it keeps the flow natural; do not skip preservation and uncertainty options.

## Round 1 — Goal and surface

Ask (via AskUserQuestion, every round includes an uncertainty option such as “I’m not sure — help me decide”):

- **Primary goal:** polish for publication, email, internal doc, chat reply, or something else?
- **Risk tolerance:** light touch (keep structure) vs. heavier rewrite (more natural at the cost of reordering).

## Round 2 — Voice and register

- **Register:** formal, neutral-professional, conversational, or deliberately terse?
- **Voice sample:** does the user want calibration from 2–3 paragraphs of their own writing? If yes, ask them to paste it before the target text.

If they provide a sample, **extract** concrete cues: average sentence length, contraction usage, hedging level, humor, first vs. third person, list vs. prose preference. Apply those cues in the rewrite; do not paste analysis unless they ask.

## Round 3 — Preservation (non-negotiable)

Confirm what must stay verbatim or numerically exact:

- Names, dates, numbers, quotes, legal/claims language, product names, URLs.
- Required terminology (brand glossary, regulated terms).

If the user is unsure, default to **strict preservation** for anything that looks like a fact, figure, or citation.

## Round 4 — Format and locale

- Output shape: single block, markdown with headings, bullets allowed or discouraged.
- **Locale:** spelling and punctuation convention (e.g. US vs. UK English) when the user cares; if unspecified, match the source text.

## Round 5 — Chatbot and meta artifacts

Ask whether to strip assistant-style wrappers (“Great question”, “I hope this helps”, “Let me know if…”). Default: **strip** unless the text is meant to stay inside a chatbot UI verbatim.
