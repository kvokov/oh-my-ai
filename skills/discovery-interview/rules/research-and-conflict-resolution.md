---
name: research-and-conflict-resolution
description: Phases 3–4 — research loops when uncertain and structured conflict resolution
---

# Research loops and conflict resolution

## Phase 3: Research loops

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

## Phase 4: Conflict resolution

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
