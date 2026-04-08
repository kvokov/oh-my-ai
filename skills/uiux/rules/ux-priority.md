---
name: ux-priority
description: Priority-ordered UX and UI quality categories with concrete checks and anti-patterns.
---

# UX priority table

Process categories **1 → 10** before debating visual flourishes. Skip lower tiers when timeboxed only if the user explicitly accepts the risk.

| Priority | Category | Impact | Key checks (must have) | Anti-patterns (avoid) |
|----------|----------|--------|------------------------|------------------------|
| 1 | Accessibility | CRITICAL | Contrast ≥4.5:1 body text; keyboard/focus order; labels; meaningful alt; `aria-*` / native equivalents; reduced motion | Icon-only controls without names; removing focus indicators |
| 2 | Touch & interaction | CRITICAL | 44×44pt / 48×48dp targets; spacing between targets; loading feedback; error near field | Hover-only primary actions on web; zero feedback on tap |
| 3 | Performance | HIGH | Image optimization; lazy below-fold; reserve space (CLS); list virtualization for long lists | Animating layout properties that thrash; unbounded re-renders |
| 4 | Style selection | HIGH | One coherent style system; SVG/icons not emoji; shadows/radius aligned to style | Mixing unrelated visual languages on one surface |
| 5 | Layout & responsive | HIGH | Mobile-first breakpoints; no horizontal scroll on small viewports; readable line length | Fixed widths that break small screens; content under fixed bars |
| 6 | Typography & color | MEDIUM | Base ~16px body; line-height ~1.5–1.75; semantic color tokens | Gray-on-gray body; raw hex sprinkled in components |
| 7 | Animation | MEDIUM | 150–300ms micro-interactions; transform/opacity; respect reduced-motion | Decorative motion; blocking input during animation |
| 8 | Forms & feedback | MEDIUM | Visible labels; inline errors; disabled/loading states; destructive confirmations | Placeholder-as-label; errors only at top of page |
| 9 | Navigation | HIGH | Predictable back; deep links/URLs on web; clear active state; modal escape | Primary flows trapped in modals; overloaded nav |
| 10 | Charts & data | LOW | Correct chart for data; legend; non-color cues; table or summary for a11y | Color-only meaning; unreadable mobile axes |

## Quick reference cues

**Accessibility:** contrast, focus-visible, heading order, `aria-live` for toasts/errors where appropriate, no information by color alone.

**Performance (web):** code-split routes, memoize hot lists, defer non-critical scripts, prefer `transform`/`opacity` for motion.

**Performance (RN/Flutter):** stable list keys, avoid rebuilding huge subtrees, virtualize long lists, profile jank on device.

**Tailwind:** use spacing/typography scales consistently; extract repeated patterns into components, not copy-paste class strings without structure.

**Next.js:** align client boundaries with interactivity; avoid shipping heavy client trees for static content.
