---
name: design-reference
description: Self-contained guidance for style, color, typography, landing structure, charts, and performance aligned to common product types.
---

# Design reference

Use this file when choosing **visual language**, **page structure**, or **data visualization** before writing code. It complements [ux-priority](./ux-priority.md) (quality order) and [stacks](./stacks.md) (framework fidelity).

## Style and product fit

| Product shape | Typical density | Motion | Visual emphasis |
|---------------|-----------------|--------|-----------------|
| **SaaS / admin / dashboard** | High information density; tables and filters | Subtle; state transitions only | Clarity, scanability, consistent components |
| **Marketing / landing** | Breathing room; strong hierarchy | Moderate for storytelling | Brand, hero, proof, single primary CTA per section |
| **E-commerce / catalog** | Grid-forward; fast scanning | Light | Imagery, price legibility, trust signals |
| **Portfolio / creative** | Variable; editorial layouts | Can be expressive | Work-first; avoid ornament that hides content |
| **Mobile utility** | Task-first; minimal chrome | Short, functional | Touch, one-handed reach, progress for multi-step |

Pick **style keywords** (e.g. minimal, playful, premium, technical) and hold them constant across the surface. Mixing unrelated styles (e.g. brutalist hero + neumorphic cards) reads as accidental unless deliberately branded.

## Color systems

1. Define **semantic roles** first: primary, secondary, success, warning, danger, surface, on-surface, muted, border, ring/focus.
2. Pair **foreground/background** to WCAG **AA** minimum (4.5:1 body; 3:1 large UI text and non-text focus indicators where applicable).
3. Design **light and dark** together; do not assume light-mode tokens invert cleanly.
4. **Functional states** (error, success) need icon or text — not color alone.
5. Prefer **tokens** (CSS variables, theme objects, `ThemeData`) over raw hex in leaf components.

## Typography

- Use **one heading family and one body family** (or one variable family with distinct roles); avoid stacking many typefaces.
- Establish a **modular scale** (e.g. 12 / 14 / 16 / 18 / 20 / 24 / 32) and reuse steps; body **≥16px** equivalent on mobile web to avoid unwanted zoom.
- **Line height** ~1.5–1.75 for body; **line length** ~60–75 characters on wide layouts.
- **Tabular figures** for numbers in tables and dashboards when available.

## Landing and key marketing sections

A solid default **section order** (trim or reorder to match brand):

1. **Hero** — primary value + one primary CTA + optional secondary.
2. **Social proof** — logos, metrics, or testimonials early if trust is a conversion blocker.
3. **Problem / outcome** — short, concrete; avoid generic filler.
4. **Features or benefits** — scannable blocks; parallel structure.
5. **Pricing or plans** (if applicable) — clear comparison; highlight recommended tier.
6. **FAQ** — objections and support paths.
7. **Footer** — legal, contact, secondary nav.

Keep **one clear primary action** per viewport section; secondary actions stay visually subordinate.

## Charts and data visualization

| Data intent | Strong default | Watch outs |
|-------------|----------------|------------|
| Trend over time | Line or area | Too many series; unreadable mobile |
| Compare categories | Bar (horizontal on small screens) | Truncated labels; 3D effects |
| Part-to-whole | Stacked bar or donut with few segments | Pie/donut with **>5** categories — prefer bar |
| Distribution | Histogram, box plot (analytics) | Over-binning or misleading scales |

Always: **legend or direct labels**, **tooltip or value on focus**, **non-color differentiation** (patterns, shape), **table or summary for screen readers**, **empty and loading states**, **respect reduced motion** (animate only if it aids comprehension).

## Dense forms and settings screens

- **Progressive disclosure** for advanced options.
- **Visible labels**; placeholders are hints only.
- **Errors** next to fields; summarize multiple errors with links when needed.
- **Destructive actions** require confirmation; separate from routine navigation.

## Performance notes (high level)

**Web (React / Next.js):** route- or component-level code splitting for heavy widgets; optimize images (responsive, modern formats); stable layout for media; avoid unnecessary client JS for static regions; memoize hot list items and stable callbacks when profiling shows benefit.

**React Native:** list virtualization for long feeds; avoid anonymous functions in `renderItem` if it causes churn; `memo` on row components when measured; image sizing and caching awareness.

**Flutter:** prefer `const` constructors where possible; avoid rebuilding large `build` subtrees; use `ListView.builder` for long lists; keep animations on compositor-friendly properties.

When unsure, **measure** (Lighthouse, React DevTools Profiler, Flutter DevTools) rather than pre-optimizing.
