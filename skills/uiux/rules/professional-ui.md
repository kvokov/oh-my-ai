---
name: professional-ui
description: High-signal polish rules for icons, interaction, theming, and layout on web and native.
---

# Professional UI polish

These items separate **competent** UI from **fragile** UI. Apply on every delivery pass.

## Icons and visuals

- Use **one vector icon family**; keep stroke weight consistent.
- Do **not** use emoji as nav or system icons.
- Define **icon size tokens** (e.g. 16 / 20 / 24) and stick to them.
- Pressed states must not **shift layout** (use opacity, scale within bounds, or elevation).

## Interaction

- **Web:** visible `:focus-visible`; primary actions work with keyboard.
- **Native:** meaningful `accessibilityLabel` / Flutter semantics; hit targets meet minimums.
- **Async:** show loading on triggers; disable double-submit where destructive.

## Light / dark

- Define **semantic tokens** per theme; verify contrast independently per theme.
- Modals/sheets: **scrim** strong enough to separate foreground (typically ~40–60% black for light scrims — tune per brand).

## Layout

- **Web:** respect viewport meta; avoid disabling zoom; watch for `100vh` issues — prefer `dvh`/`svh` patterns where appropriate.
- **Native:** safe areas for fixed chrome; scroll content must clear tab bars and CTAs.
- Maintain **spacing rhythm** (4/8-based or tokenized equivalent).

## Pre-delivery quick scan

- [ ] Examples and library APIs match the **project’s actual framework** (no gratuitous multi-stack dumps)
- [ ] Contrast and focus states checked
- [ ] Touch targets and safe areas (native) or keyboard path (web)
- [ ] Loading / error / empty states present where data is async
- [ ] Reduced motion respected for decorative animation
