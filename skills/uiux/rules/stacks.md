---
name: stacks
description: Primary stack focus (React, Next.js, Tailwind, RN, Flutter) and how to handle other frameworks without scope creep.
---

# Stacks

## Primary focus (default depth)

This skill **goes deepest** on these stacks so guidance stays actionable instead of a multi-framework catalog:

| Stack | Role |
|-------|------|
| **React** | Components, state, composition, performance habits |
| **Next.js** | Routing, SSR/RSC boundaries, images, fonts, bundle splitting |
| **Tailwind CSS** | Utility layout, tokens via config/theme, responsive patterns |
| **React Native** | Screens, lists, navigation touchpoints, platform APIs (`accessibilityLabel`, safe area) |
| **Flutter** | Widgets, `ThemeData`, Material/Cupertino idioms, layout |

**Default behavior:** Prefer examples, API names, and file-path hints that match this set **when the user does not specify a stack** or when the repo clearly uses one of them.

## Other stacks (Vue, Svelte, SwiftUI, and others)

When the **repository or user** is clearly on another framework:

- **Follow the real stack.** Use that framework’s components, routing, styling, and accessibility APIs; do not force a rewrite into React/Flutter “just because.”
- **Reuse the same UX priorities** from [ux-priority](./ux-priority.md) (contrast, focus, touch targets, motion, forms, navigation) — they are mostly framework-agnostic.
- **Avoid scope creep:** do not dump parallel examples for every framework; give **one** stack-faithful implementation path unless the user asks to compare stacks.
- If you are unsure which stack applies, **inspect the repo** (`package.json`, `vite.config`, `nuxt.config`, `svelte.config`, Xcode project, etc.) and align with what you find.

## Stack-specific reminders (primary-focus stacks)

### React + Next.js + Tailwind (web)

- Prefer **semantic HTML**, visible labels, and focus-visible styles; do not remove focus rings for aesthetics.
- Use **Next.js** `Image` and font loading patterns where applicable; avoid layout shift (explicit dimensions or aspect ratio).
- Use **Tailwind** design tokens (CSS variables or `theme.extend`) instead of scattering raw hex in JSX.

### React Native

- Minimum touch targets **44×44 pt** (iOS) / **48×48 dp** (Android); use `hitSlop` when the visual is smaller.
- Respect **safe areas** for fixed headers, tab bars, and bottom CTAs.
- Prefer **vector icons** (e.g. SF Symbols via compatible libs, or a single icon family) — not emoji as structural icons.

### Flutter

- Map colors and type to **`ThemeData`** / component themes; avoid hardcoded `Color(0xFF...)` in leaves unless tokens demand it.
- Use platform-adaptive patterns (Material vs Cupertino) when the product targets both mobile OS idioms.

## When the repo stack is unclear

1. Inspect `package.json`, `app/` or `src/`, and config (`next.config`, `tailwind.config`, `pubspec.yaml`, framework-specific configs).
2. If multiple stacks appear (e.g. Next + RN monorepo), scope recommendations to the **file or package** the user is editing.
