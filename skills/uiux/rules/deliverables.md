---
name: deliverables
description: File-first outputs — design system master, page overrides, tokens, and component specs.
---

# File-first deliverables

When the task changes how a product **looks, feels, moves, or behaves** for users, **write or update files** in the target project (do not rely on chat-only advice for non-trivial work).

## 1. `design-system/MASTER.md` (global source of truth)

Create at the **project root** unless the repo already stores design docs elsewhere — then match existing layout and link from README or `AGENTS.md` if present.

Use this skeleton:

```markdown
# Design system — MASTER

## Product context
- Product type:
- Primary audience:
- Platforms: (web / iOS / Android — via RN or Flutter)

## Stack
Record the **actual** framework(s) for this product (e.g. React, Next.js, Tailwind, Vue, Svelte, SwiftUI, React Native, Flutter). If contributors switch stacks, note constraints and token mapping here.

## Visual direction
- Style keywords: (e.g. minimal, dense dashboard, playful consumer)
- Elevation/shape: (radius scale, shadow tiers)
- Icon set: (e.g. Lucide, Heroicons, Material Symbols — one family)

## Semantic tokens
- Color roles: primary, secondary, danger, success, surface, on-surface, border, muted
- Typography roles: display, title, body, label, code
- Spacing scale: (e.g. 4/8 base)
- Motion: durations and easing tokens; reduced-motion behavior

## Layout rules
- Breakpoints: ...
- Max content width: ...
- Grid / spacing rhythm: ...

## Components (inventory)
For each: states (default, hover/focus, active, disabled, loading), a11y notes, and stack-specific implementation hints.

## Anti-patterns (project-specific)
- ...

## Review checklist
Link or embed the pre-delivery checklist from the main skill.
```

## 2. `design-system/pages/<page-slug>.md` (optional overrides)

Use when a screen **deviates** from MASTER (e.g. marketing landing vs. dense admin).

```markdown
# Page override — <page-slug>

## Deviations from MASTER
- ...

## Layout specifics
- ...

## Content priorities (mobile → desktop)
- ...
```

## 3. `design-system/tokens` (optional but recommended)

Prefer **one** of:

- **Tailwind:** `tailwind.config` + CSS variables in `:root` / `.dark`
- **RN:** `theme.ts` or platform design-tokens module consumed by styled components / StyleSheet factories
- **Flutter:** `ThemeData` + `ColorScheme` in a single module

Document the mapping in `MASTER.md` under **Semantic tokens**.

## 4. Component spec (`design-system/components/<name>.md`)

For new or refactored components:

```markdown
# Component — <Name>

## Purpose & anatomy
## Props / API
## States & interactions
## Accessibility
## Stack notes (React / Next / Tailwind / RN / Flutter)
## References in codebase
```

## Retrieval prompt (for later sessions)

When files exist, tell the user (or the agent) to load:

1. `design-system/MASTER.md`
2. `design-system/pages/<page>.md` if present
3. Relevant `design-system/components/<name>.md`

Page overrides **win** over MASTER for conflicting rules.
